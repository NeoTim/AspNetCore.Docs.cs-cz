---
title: Použití Center v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se používat centra v ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: bd7432fc29d0cda003abed1f0e522bdddf2e4efc
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022209"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="c9c11-103">Použití Center v SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c9c11-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="c9c11-104">Od [Rachel Appel](https://twitter.com/rachelappel) a [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="c9c11-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="c9c11-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c9c11-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-no-locsignalr-hub"></a><span data-ttu-id="c9c11-106">Co je SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="c9c11-106">What is a SignalR hub</span></span>

<span data-ttu-id="c9c11-107">SignalRRozhraní API Center umožňuje volat metody v připojených klientech ze serveru.</span><span class="sxs-lookup"><span data-stu-id="c9c11-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="c9c11-108">V kódu serveru definujete metody, které jsou volány klientem.</span><span class="sxs-lookup"><span data-stu-id="c9c11-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="c9c11-109">V kódu klienta definujete metody, které jsou volány ze serveru.</span><span class="sxs-lookup"><span data-stu-id="c9c11-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="c9c11-110">SignalRpostará se o všechno na pozadí, které umožňuje komunikaci mezi klientem a serverem od klientů po straně klienta.</span><span class="sxs-lookup"><span data-stu-id="c9c11-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-no-locsignalr-hubs"></a><span data-ttu-id="c9c11-111">Konfigurovat SignalR centra</span><span class="sxs-lookup"><span data-stu-id="c9c11-111">Configure SignalR hubs</span></span>

<span data-ttu-id="c9c11-112">SignalRMiddleware vyžaduje některé služby, které jsou nakonfigurované voláním `services.AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c9c11-113">Při přidávání SignalR funkcí do aplikace ASP.NET Core nastavte SignalR trasy voláním `endpoint.MapHub` ve `Startup.Configure` `app.UseEndpoints` zpětném volání metody.</span><span class="sxs-lookup"><span data-stu-id="c9c11-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c9c11-114">Při přidávání SignalR funkcí do aplikace ASP.NET Core nastavte SignalR trasy voláním `app.UseSignalR` `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="c9c11-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="c9c11-115">Vytváření a používání Center</span><span class="sxs-lookup"><span data-stu-id="c9c11-115">Create and use hubs</span></span>

<span data-ttu-id="c9c11-116">Vytvořte centrum deklarováním třídy, která dědí z `Hub` a přidejte do ní veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="c9c11-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="c9c11-117">Klienti mohou volat metody, které jsou definovány jako `public` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="c9c11-118">Můžete zadat návratový typ a parametry, včetně složitých typů a polí, stejně jako v libovolné metodě jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="c9c11-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="c9c11-119">SignalRzpracovává serializaci a deserializaci komplexních objektů a polí ve vašich parametrech a návratových hodnotách.</span><span class="sxs-lookup"><span data-stu-id="c9c11-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="c9c11-120">Rozbočovače jsou přechodné:</span><span class="sxs-lookup"><span data-stu-id="c9c11-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="c9c11-121">Neukládat stav ve vlastnosti ve třídě hub.</span><span class="sxs-lookup"><span data-stu-id="c9c11-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="c9c11-122">Každé volání metody centra je spuštěno na nové instanci centra.</span><span class="sxs-lookup"><span data-stu-id="c9c11-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="c9c11-123">Použijte `await` při volání asynchronních metod, které jsou závislé na době zachování rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c9c11-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="c9c11-124">Například metoda, jako například, `Clients.All.SendAsync(...)` může selhat, pokud je volána bez `await` a metoda centra se dokončí před `SendAsync` dokončením.</span><span class="sxs-lookup"><span data-stu-id="c9c11-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="c9c11-125">Objekt kontextu</span><span class="sxs-lookup"><span data-stu-id="c9c11-125">The Context object</span></span>

<span data-ttu-id="c9c11-126">`Hub`Třída má `Context` vlastnost, která obsahuje následující vlastnosti s informacemi o připojení:</span><span class="sxs-lookup"><span data-stu-id="c9c11-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="c9c11-127">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c9c11-127">Property</span></span> | <span data-ttu-id="c9c11-128">Popis</span><span class="sxs-lookup"><span data-stu-id="c9c11-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="c9c11-129">Získá jedinečné ID pro připojení, které přiřadí SignalR .</span><span class="sxs-lookup"><span data-stu-id="c9c11-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="c9c11-130">Pro každé připojení existuje jedno ID připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="c9c11-131">Získá [identifikátor uživatele](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="c9c11-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="c9c11-132">Ve výchozím nastavení SignalR používá nástroj `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="c9c11-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="c9c11-133">Získá `ClaimsPrincipal` přidružený k aktuálnímu uživateli.</span><span class="sxs-lookup"><span data-stu-id="c9c11-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="c9c11-134">Získá kolekci klíč/hodnota, která se dá použít ke sdílení dat v rámci rozsahu tohoto připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="c9c11-135">Data mohou být uložena v této kolekci a budou uchována pro připojení v různých voláních metod rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="c9c11-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="c9c11-136">Získá kolekci funkcí dostupných na připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="c9c11-137">Ve většině scénářů teď není tato kolekce potřebná, takže ještě není popsána podrobněji.</span><span class="sxs-lookup"><span data-stu-id="c9c11-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="c9c11-138">Získá `CancellationToken` upozornění, když je připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="c9c11-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="c9c11-139">`Hub.Context`obsahuje také následující metody:</span><span class="sxs-lookup"><span data-stu-id="c9c11-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="c9c11-140">Metoda</span><span class="sxs-lookup"><span data-stu-id="c9c11-140">Method</span></span> | <span data-ttu-id="c9c11-141">Popis</span><span class="sxs-lookup"><span data-stu-id="c9c11-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="c9c11-142">Vrátí `HttpContext` pro připojení, nebo `null` Pokud připojení není přidruženo k požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="c9c11-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="c9c11-143">V případě připojení HTTP můžete použít tuto metodu k získání informací, jako jsou hlavičky HTTP a řetězce dotazů.</span><span class="sxs-lookup"><span data-stu-id="c9c11-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="c9c11-144">Přeruší připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="c9c11-145">Objekt klienti</span><span class="sxs-lookup"><span data-stu-id="c9c11-145">The Clients object</span></span>

<span data-ttu-id="c9c11-146">`Hub`Třída má `Clients` vlastnost, která obsahuje následující vlastnosti pro komunikaci mezi serverem a klientem:</span><span class="sxs-lookup"><span data-stu-id="c9c11-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="c9c11-147">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="c9c11-147">Property</span></span> | <span data-ttu-id="c9c11-148">Popis</span><span class="sxs-lookup"><span data-stu-id="c9c11-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="c9c11-149">Volá metodu na všech připojených klientech.</span><span class="sxs-lookup"><span data-stu-id="c9c11-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="c9c11-150">Volá metodu na klientovi, který vyvolal metodu hub.</span><span class="sxs-lookup"><span data-stu-id="c9c11-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="c9c11-151">Volá metodu na všech připojených klientech s výjimkou klienta, který metodu vyvolal.</span><span class="sxs-lookup"><span data-stu-id="c9c11-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="c9c11-152">`Hub.Clients`obsahuje také následující metody:</span><span class="sxs-lookup"><span data-stu-id="c9c11-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="c9c11-153">Metoda</span><span class="sxs-lookup"><span data-stu-id="c9c11-153">Method</span></span> | <span data-ttu-id="c9c11-154">Popis</span><span class="sxs-lookup"><span data-stu-id="c9c11-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="c9c11-155">Volá metodu na všech připojených klientech s výjimkou zadaných připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="c9c11-156">Volá metodu pro určitého připojeného klienta.</span><span class="sxs-lookup"><span data-stu-id="c9c11-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="c9c11-157">Volá metodu pro konkrétní připojené klienty.</span><span class="sxs-lookup"><span data-stu-id="c9c11-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="c9c11-158">Volá metodu pro všechna připojení v zadané skupině.</span><span class="sxs-lookup"><span data-stu-id="c9c11-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="c9c11-159">Volá metodu pro všechna připojení v zadané skupině s výjimkou zadaných připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="c9c11-160">Volá metodu pro více skupin připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="c9c11-161">Volá metodu pro skupinu připojení s výjimkou klienta, který vyvolal metodu hub.</span><span class="sxs-lookup"><span data-stu-id="c9c11-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="c9c11-162">Volá metodu pro všechna připojení přidružená ke konkrétnímu uživateli.</span><span class="sxs-lookup"><span data-stu-id="c9c11-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="c9c11-163">Volá metodu pro všechna připojení přidružená k určeným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="c9c11-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="c9c11-164">Každá vlastnost nebo metoda v předchozích tabulkách vrátí objekt s `SendAsync` metodou.</span><span class="sxs-lookup"><span data-stu-id="c9c11-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="c9c11-165">`SendAsync`Metoda umožňuje zadejte název a parametry metody klienta, které mají být volány.</span><span class="sxs-lookup"><span data-stu-id="c9c11-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="c9c11-166">Odesílání zpráv klientům</span><span class="sxs-lookup"><span data-stu-id="c9c11-166">Send messages to clients</span></span>

<span data-ttu-id="c9c11-167">Chcete-li volat konkrétní klienty, použijte vlastnosti `Clients` objektu.</span><span class="sxs-lookup"><span data-stu-id="c9c11-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="c9c11-168">V následujícím příkladu jsou tři metody centra:</span><span class="sxs-lookup"><span data-stu-id="c9c11-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="c9c11-169">`SendMessage`odešle zprávu všem připojeným klientům pomocí `Clients.All` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="c9c11-170">`SendMessageToCaller`pošle zprávu zpět volajícímu pomocí `Clients.Caller` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="c9c11-171">`SendMessageToGroups`pošle zprávu všem klientům ve `SignalR Users` skupině.</span><span class="sxs-lookup"><span data-stu-id="c9c11-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="c9c11-172">Rozbočovače silného typu</span><span class="sxs-lookup"><span data-stu-id="c9c11-172">Strongly typed hubs</span></span>

<span data-ttu-id="c9c11-173">Nevýhodou použití `SendAsync` je, že při určení metody klienta, která má být volána, spoléhá na řetězec Magic.</span><span class="sxs-lookup"><span data-stu-id="c9c11-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="c9c11-174">Tím zůstane kód otevřený pro běhové chyby, pokud je název metody špatně napsaný nebo chybí v klientovi.</span><span class="sxs-lookup"><span data-stu-id="c9c11-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="c9c11-175">Alternativa k použití `SendAsync` je silného typu `Hub` s <xref:Microsoft.AspNetCore.SignalR.Hub%601> .</span><span class="sxs-lookup"><span data-stu-id="c9c11-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="c9c11-176">V následujícím příkladu byly `ChatHub` klientské metody extrahovány do rozhraní s názvem `IChatClient` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="c9c11-177">Toto rozhraní lze použít k refaktorování předchozího `ChatHub` příkladu.</span><span class="sxs-lookup"><span data-stu-id="c9c11-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="c9c11-178">Pomocí této `Hub<IChatClient>` metody lze provádět kontrolu klientských metod v době kompilace.</span><span class="sxs-lookup"><span data-stu-id="c9c11-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="c9c11-179">To brání problémům způsobeným použitím řetězců Magic, protože `Hub<T>` může poskytnout přístup pouze k metodám definovaným v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c9c11-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="c9c11-180">Použití silného typu `Hub<T>` zakáže schopnost použít `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="c9c11-181">Jakékoli metody definované v rozhraní lze i nadále definovat jako asynchronní.</span><span class="sxs-lookup"><span data-stu-id="c9c11-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="c9c11-182">Každá z těchto metod by ve skutečnosti měla vrátit `Task` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="c9c11-183">Vzhledem k tomu, že se jedná o rozhraní, nepoužívejte `async` klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="c9c11-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="c9c11-184">Například:</span><span class="sxs-lookup"><span data-stu-id="c9c11-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="c9c11-185">`Async`Přípona není z názvu metody odstraněna.</span><span class="sxs-lookup"><span data-stu-id="c9c11-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="c9c11-186">Pokud vaše metoda klienta není definovaná pomocí `.on('MyMethodAsync')` , neměli byste používat `MyMethodAsync` jako název.</span><span class="sxs-lookup"><span data-stu-id="c9c11-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="c9c11-187">Změna názvu metody centra</span><span class="sxs-lookup"><span data-stu-id="c9c11-187">Change the name of a hub method</span></span>

<span data-ttu-id="c9c11-188">Ve výchozím nastavení je název metody centra serveru název metody .NET.</span><span class="sxs-lookup"><span data-stu-id="c9c11-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="c9c11-189">Můžete však použít atribut [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) ke změně tohoto výchozího nastavení a ručně zadat název metody.</span><span class="sxs-lookup"><span data-stu-id="c9c11-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="c9c11-190">Klient by měl použít tento název namísto názvu metody .NET při volání metody.</span><span class="sxs-lookup"><span data-stu-id="c9c11-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="c9c11-191">Zpracování událostí pro připojení</span><span class="sxs-lookup"><span data-stu-id="c9c11-191">Handle events for a connection</span></span>

<span data-ttu-id="c9c11-192">SignalRRozhraní API Center poskytuje `OnConnectedAsync` `OnDisconnectedAsync` virtuálním metodám a ke správě a sledování připojení.</span><span class="sxs-lookup"><span data-stu-id="c9c11-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="c9c11-193">Přepište `OnConnectedAsync` virtuální metodu tak, aby prováděla akce, když se klient připojí k rozbočovači, jako je například přidání do skupiny.</span><span class="sxs-lookup"><span data-stu-id="c9c11-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="c9c11-194">Přepište `OnDisconnectedAsync` virtuální metodu tak, aby prováděla akce, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="c9c11-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="c9c11-195">Pokud se klient odpojí úmyslně (například voláním metody `connection.stop()` ), bude `exception` parametr `null` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="c9c11-196">Pokud je však klient odpojen z důvodu chyby (například selhání sítě), `exception` parametr bude obsahovat výjimku popisující selhání.</span><span class="sxs-lookup"><span data-stu-id="c9c11-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="c9c11-197">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="c9c11-197">Handle errors</span></span>

<span data-ttu-id="c9c11-198">Výjimky vyvolané ve vašich metodách centra jsou odesílány klientovi, který tuto metodu vyvolal.</span><span class="sxs-lookup"><span data-stu-id="c9c11-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="c9c11-199">V klientovi jazyka JavaScript `invoke` vrátí metoda [příslib JavaScriptu](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="c9c11-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="c9c11-200">Když klient obdrží chybu s obslužnou rutinou připojenou k Promise pomocí `catch` , je vyvolána a předána jako objekt JavaScriptu `Error` .</span><span class="sxs-lookup"><span data-stu-id="c9c11-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="c9c11-201">Pokud vaše centrum vyvolá výjimku, připojení se nezavřou.</span><span class="sxs-lookup"><span data-stu-id="c9c11-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="c9c11-202">Ve výchozím nastavení SignalR vrátí klientovi obecnou chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="c9c11-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="c9c11-203">Například:</span><span class="sxs-lookup"><span data-stu-id="c9c11-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="c9c11-204">Neočekávané výjimky často obsahují citlivé informace, jako je třeba název databázového serveru v výjimce aktivované při neúspěchu připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="c9c11-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="c9c11-205">SignalRnezveřejňuje tyto podrobné chybové zprávy ve výchozím nastavení jako bezpečnostní opatření.</span><span class="sxs-lookup"><span data-stu-id="c9c11-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="c9c11-206">Další informace o tom, proč se potlačí podrobnosti o výjimce, najdete v článku věnovaném [bezpečnostním hlediskům](xref:signalr/security#exceptions) .</span><span class="sxs-lookup"><span data-stu-id="c9c11-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="c9c11-207">Pokud máte výjimečnou podmínku *, kterou chcete* rozšířit na klienta, můžete použít `HubException` třídu.</span><span class="sxs-lookup"><span data-stu-id="c9c11-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="c9c11-208">Pokud vyvoláte `HubException` metodu z vašeho centra, SignalR **aplikace** pošle celou zprávu klientovi, který nezůstane beze změny.</span><span class="sxs-lookup"><span data-stu-id="c9c11-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="c9c11-209">SignalRpouze odesílá `Message` vlastnost výjimky klientovi.</span><span class="sxs-lookup"><span data-stu-id="c9c11-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="c9c11-210">Trasování zásobníku a další vlastnosti výjimky nejsou pro klienta k dispozici.</span><span class="sxs-lookup"><span data-stu-id="c9c11-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c9c11-211">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="c9c11-211">Related resources</span></span>

* [<span data-ttu-id="c9c11-212">Úvod k ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="c9c11-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="c9c11-213">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c9c11-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c9c11-214">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="c9c11-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
