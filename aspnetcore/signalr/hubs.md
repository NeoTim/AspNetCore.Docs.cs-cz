---
title: Použití rozbočovače signalr technologie ASP.NET Core
author: bradygaster
description: Další informace o použití rozbočovače signalr technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: f037c1f6fd7ef773b8e7b2fd4fdf6e28222c441a
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327258"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a><span data-ttu-id="f822c-103">Použití rozbočovače signalr pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f822c-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="f822c-104">Podle [Rachel Appel](https://twitter.com/rachelappel) a [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="f822c-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="f822c-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f822c-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-signalr-hub"></a><span data-ttu-id="f822c-106">Co je rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="f822c-106">What is a SignalR hub</span></span>

<span data-ttu-id="f822c-107">Rozhraní API pro rozbočovače SignalR můžete volat metody pro připojené klienty ze serveru.</span><span class="sxs-lookup"><span data-stu-id="f822c-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="f822c-108">V serverovém kódu definovat metody, které jsou volány klientem.</span><span class="sxs-lookup"><span data-stu-id="f822c-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="f822c-109">V kódu klienta můžete definovat metody, které jsou volány ze serveru.</span><span class="sxs-lookup"><span data-stu-id="f822c-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="f822c-110">Funkce SignalR se postará o všechno, co na pozadí, které umožňuje komunikaci klienta se serverem a server klient v reálném čase.</span><span class="sxs-lookup"><span data-stu-id="f822c-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-signalr-hubs"></a><span data-ttu-id="f822c-111">Konfigurace rozbočovače SignalR</span><span class="sxs-lookup"><span data-stu-id="f822c-111">Configure SignalR hubs</span></span>

<span data-ttu-id="f822c-112">SignalR middleware vyžaduje některé služby, které jsou nakonfigurované pomocí volání `services.AddSignalR`.</span><span class="sxs-lookup"><span data-stu-id="f822c-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

<span data-ttu-id="f822c-113">Při přidávání funkce SignalR pro aplikace ASP.NET Core, nastavit trasy SignalR voláním `app.UseSignalR` v `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="f822c-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a><span data-ttu-id="f822c-114">Vytvoření a použití rozbočovače</span><span class="sxs-lookup"><span data-stu-id="f822c-114">Create and use hubs</span></span>

<span data-ttu-id="f822c-115">Vytvoření centra deklarováním třídy, která dědí z `Hub`a přidejte do ní veřejné metody.</span><span class="sxs-lookup"><span data-stu-id="f822c-115">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="f822c-116">Klienti mohou volat metody, které jsou definovány jako `public`.</span><span class="sxs-lookup"><span data-stu-id="f822c-116">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="f822c-117">Můžete určit návratový typ a parametry, včetně komplexní typy a pole, stejně jako v jakékoli metodě jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="f822c-117">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="f822c-118">Funkce SignalR zpracovává serializace a deserializace komplexních objektů a polí v parametry a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f822c-118">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="f822c-119">Centra jsou přechodné:</span><span class="sxs-lookup"><span data-stu-id="f822c-119">Hubs are transient:</span></span>
>
> * <span data-ttu-id="f822c-120">Neukládejte stav do vlastnosti u třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f822c-120">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="f822c-121">Každé volání metody rozbočovače, je proveden v nové instanci rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="f822c-121">Every hub method call is executed on a new hub instance.</span></span>  
> * <span data-ttu-id="f822c-122">Použití `await` při volání asynchronní metody, které jsou závislé na rozbočovači zůstává aktivní.</span><span class="sxs-lookup"><span data-stu-id="f822c-122">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="f822c-123">Například metoda jako `Clients.All.SendAsync(...)` může selhat, pokud je volána bez `await` a metody rozbočovače nedokončí, před `SendAsync` dokončí.</span><span class="sxs-lookup"><span data-stu-id="f822c-123">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="f822c-124">Objekt kontextu</span><span class="sxs-lookup"><span data-stu-id="f822c-124">The Context object</span></span>

<span data-ttu-id="f822c-125">`Hub` Třída nemá `Context` vlastnost, která obsahuje následující vlastnosti s informacemi o připojení:</span><span class="sxs-lookup"><span data-stu-id="f822c-125">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="f822c-126">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="f822c-126">Property</span></span> | <span data-ttu-id="f822c-127">Popis</span><span class="sxs-lookup"><span data-stu-id="f822c-127">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="f822c-128">Získá jedinečný ID pro připojení, přiřazené systémem SignalR.</span><span class="sxs-lookup"><span data-stu-id="f822c-128">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="f822c-129">Existuje jedno ID připojení pro každé připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-129">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="f822c-130">Získá [identifikátor uživatele](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="f822c-130">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="f822c-131">Ve výchozím nastavení, používá SignalR `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidružené k připojení jako identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="f822c-131">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="f822c-132">Získá `ClaimsPrincipal` spojené s aktuálním uživatelem.</span><span class="sxs-lookup"><span data-stu-id="f822c-132">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="f822c-133">Získá kolekci klíč/hodnota, která umožňuje sdílet data v rámci oboru pro toto připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-133">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="f822c-134">Data mohou být uložena v této kolekci a se zachová připojení napříč volání metod rozbočovače na jiný.</span><span class="sxs-lookup"><span data-stu-id="f822c-134">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="f822c-135">Získá kolekci funkcí, které jsou k dispozici na připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-135">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="f822c-136">Teď není potřeba tuto kolekci ve většině scénářů, takže ho není podrobně popsány v ještě.</span><span class="sxs-lookup"><span data-stu-id="f822c-136">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="f822c-137">Získá `CancellationToken` , která upozorní, když je připojení přerušeno.</span><span class="sxs-lookup"><span data-stu-id="f822c-137">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="f822c-138">`Hub.Context` obsahuje také následující metody:</span><span class="sxs-lookup"><span data-stu-id="f822c-138">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="f822c-139">Metoda</span><span class="sxs-lookup"><span data-stu-id="f822c-139">Method</span></span> | <span data-ttu-id="f822c-140">Popis</span><span class="sxs-lookup"><span data-stu-id="f822c-140">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="f822c-141">Vrátí `HttpContext` pro připojení, nebo `null` Pokud připojení není přidružený požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="f822c-141">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="f822c-142">Pro připojení prostřednictvím protokolu HTTP můžete použít tuto metodu se získat informace, jako jsou hlavičky protokolu HTTP a řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="f822c-142">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="f822c-143">Zruší připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-143">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="f822c-144">Objekt klientů</span><span class="sxs-lookup"><span data-stu-id="f822c-144">The Clients object</span></span>

<span data-ttu-id="f822c-145">`Hub` Třída nemá `Clients` vlastnost, která obsahuje následující vlastnosti pro komunikaci mezi serverem a klientem:</span><span class="sxs-lookup"><span data-stu-id="f822c-145">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="f822c-146">Vlastnost</span><span class="sxs-lookup"><span data-stu-id="f822c-146">Property</span></span> | <span data-ttu-id="f822c-147">Popis</span><span class="sxs-lookup"><span data-stu-id="f822c-147">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="f822c-148">Volá metodu na všechny připojené klienty</span><span class="sxs-lookup"><span data-stu-id="f822c-148">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="f822c-149">Volá metodu na straně klienta, který volal metodu rozbočovače na</span><span class="sxs-lookup"><span data-stu-id="f822c-149">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="f822c-150">Volá metodu na všechny připojené klienty kromě klienta, který volal metodu</span><span class="sxs-lookup"><span data-stu-id="f822c-150">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="f822c-151">`Hub.Clients` obsahuje také následující metody:</span><span class="sxs-lookup"><span data-stu-id="f822c-151">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="f822c-152">Metoda</span><span class="sxs-lookup"><span data-stu-id="f822c-152">Method</span></span> | <span data-ttu-id="f822c-153">Popis</span><span class="sxs-lookup"><span data-stu-id="f822c-153">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="f822c-154">Volá metodu na všechny připojené klienty s výjimkou zadaných připojení</span><span class="sxs-lookup"><span data-stu-id="f822c-154">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="f822c-155">Volá metodu pro konkrétní připojení klienta</span><span class="sxs-lookup"><span data-stu-id="f822c-155">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="f822c-156">Volá metodu pro konkrétní připojených klientů</span><span class="sxs-lookup"><span data-stu-id="f822c-156">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="f822c-157">Volá metodu pro všechna připojení v určené skupině</span><span class="sxs-lookup"><span data-stu-id="f822c-157">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="f822c-158">Volá metodu pro všechna připojení v určené skupině s výjimkou zadaných připojení</span><span class="sxs-lookup"><span data-stu-id="f822c-158">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="f822c-159">Volá metodu pro více skupin pro připojení</span><span class="sxs-lookup"><span data-stu-id="f822c-159">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="f822c-160">Volá metodu pro připojení s výjimkou klienta, který volal metodu rozbočovače na skupinu</span><span class="sxs-lookup"><span data-stu-id="f822c-160">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="f822c-161">Volá metodu pro všechna připojení, které jsou spojené s konkrétním uživatelem</span><span class="sxs-lookup"><span data-stu-id="f822c-161">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="f822c-162">Volá metodu pro všechna připojení přidružená k určení uživatelé</span><span class="sxs-lookup"><span data-stu-id="f822c-162">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="f822c-163">Každé vlastnosti nebo metody v předchozích tabulkách vrátí objekt s `SendAsync` metoda.</span><span class="sxs-lookup"><span data-stu-id="f822c-163">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="f822c-164">`SendAsync` Metoda vám umožňuje zadat název a parametry metody volání.</span><span class="sxs-lookup"><span data-stu-id="f822c-164">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="f822c-165">Odesílání zpráv do klientů</span><span class="sxs-lookup"><span data-stu-id="f822c-165">Send messages to clients</span></span>

<span data-ttu-id="f822c-166">Chcete-li provést volání do konkrétních klientů, použijte vlastnosti `Clients` objektu.</span><span class="sxs-lookup"><span data-stu-id="f822c-166">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="f822c-167">V následujícím příkladu jsou tři metody rozbočovače:</span><span class="sxs-lookup"><span data-stu-id="f822c-167">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="f822c-168">`SendMessage` Odešle zprávu do všech připojených klientů používajících `Clients.All`.</span><span class="sxs-lookup"><span data-stu-id="f822c-168">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="f822c-169">`SendMessageToCaller` Odešle zprávu zpět do volajícího a pomocí `Clients.Caller`.</span><span class="sxs-lookup"><span data-stu-id="f822c-169">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="f822c-170">`SendMessageToGroups` Odešle zprávu pro všechny klienty v `SignalR Users` skupiny.</span><span class="sxs-lookup"><span data-stu-id="f822c-170">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="f822c-171">Silného typu rozbočovače</span><span class="sxs-lookup"><span data-stu-id="f822c-171">Strongly typed hubs</span></span>

<span data-ttu-id="f822c-172">Nevýhodou použití `SendAsync` je, že spoléhá na magický řetězec a určit metodu klienta k volání.</span><span class="sxs-lookup"><span data-stu-id="f822c-172">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="f822c-173">Kvůli tomu open kód chyby za běhu, pokud název metody je zadáno chybně nebo chybějící z klienta.</span><span class="sxs-lookup"><span data-stu-id="f822c-173">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="f822c-174">O alternativu k použití `SendAsync` silného typu, je `Hub` s <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span><span class="sxs-lookup"><span data-stu-id="f822c-174">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="f822c-175">V následujícím příkladu `ChatHub` metody klienta bylo extrahováno ven do rozhraní volá `IChatClient`.</span><span class="sxs-lookup"><span data-stu-id="f822c-175">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="f822c-176">Toto rozhraní je možné Refaktorovat předchozí `ChatHub` příklad.</span><span class="sxs-lookup"><span data-stu-id="f822c-176">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="f822c-177">Pomocí `Hub<IChatClient>` povolí kompilaci kontrolu metodu klienta.</span><span class="sxs-lookup"><span data-stu-id="f822c-177">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="f822c-178">To brání problémy způsobené pomocí magic řetězců, protože `Hub<T>` můžete poskytnout přístup k metodám definované v rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f822c-178">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="f822c-179">Pomocí silného typu `Hub<T>` zakáže možnost používat `SendAsync`.</span><span class="sxs-lookup"><span data-stu-id="f822c-179">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="f822c-180">Jakékoli metody definované v rozhraní může být stále definována jako o asynchronním.</span><span class="sxs-lookup"><span data-stu-id="f822c-180">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="f822c-181">Ve skutečnosti, každá z těchto metod by mělo vrátit `Task`.</span><span class="sxs-lookup"><span data-stu-id="f822c-181">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="f822c-182">Protože je rozhraní, nepoužívejte `async` – klíčové slovo.</span><span class="sxs-lookup"><span data-stu-id="f822c-182">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="f822c-183">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f822c-183">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="f822c-184">`Async` Přípona není odebrána z názvu metody.</span><span class="sxs-lookup"><span data-stu-id="f822c-184">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="f822c-185">Pokud vaše metoda klienta je definována s `.on('MyMethodAsync')`, neměli byste používat `MyMethodAsync` jako název.</span><span class="sxs-lookup"><span data-stu-id="f822c-185">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="f822c-186">Změnit název metody rozbočovače</span><span class="sxs-lookup"><span data-stu-id="f822c-186">Change the name of a hub method</span></span>

<span data-ttu-id="f822c-187">Výchozí název metody rozbočovače serveru je název metody rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="f822c-187">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="f822c-188">Můžete však použít [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) změnit toto výchozí nastavení a ručně zadávat název metody atributu.</span><span class="sxs-lookup"><span data-stu-id="f822c-188">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="f822c-189">Klient musí použít tento název místo názvu metody rozhraní .NET, při volání metody.</span><span class="sxs-lookup"><span data-stu-id="f822c-189">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="f822c-190">Zpracování událostí pro připojení</span><span class="sxs-lookup"><span data-stu-id="f822c-190">Handle events for a connection</span></span>

<span data-ttu-id="f822c-191">Poskytuje rozhraní API pro rozbočovače SignalR `OnConnectedAsync` a `OnDisconnectedAsync` virtuální metody pro správu a sledování připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-191">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="f822c-192">Přepsat `OnConnectedAsync` virtuální metody pro provádění akcí po připojení klienta k rozbočovači, jako je například přidávání do skupiny.</span><span class="sxs-lookup"><span data-stu-id="f822c-192">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="f822c-193">Přepsat `OnDisconnectedAsync` virtuální metody pro provádění akcí po odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="f822c-193">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="f822c-194">Pokud se klient odpojí záměrně (voláním `connection.stop()`, například), `exception` parametr bude `null`.</span><span class="sxs-lookup"><span data-stu-id="f822c-194">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="f822c-195">Nicméně, pokud je klient odpojen z důvodu chyby (jako je například selhání sítě), `exception` parametr bude obsahovat výjimku popisující chybu.</span><span class="sxs-lookup"><span data-stu-id="f822c-195">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="f822c-196">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="f822c-196">Handle errors</span></span>

<span data-ttu-id="f822c-197">Výjimky vzniklé v metodách vašeho centra se posílají klientovi, který volal metodu.</span><span class="sxs-lookup"><span data-stu-id="f822c-197">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="f822c-198">Na klientovi JavaScript `invoke` metoda vrátí hodnotu [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="f822c-198">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="f822c-199">Když klient obdrží chybu s obslužnou rutinou k němu připojit pomocí promise `catch`, ji má a předají jako JavaScript `Error` objektu.</span><span class="sxs-lookup"><span data-stu-id="f822c-199">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="f822c-200">Pokud vaše Centrum vyvolá výjimku, nebyly uzavřeny připojení.</span><span class="sxs-lookup"><span data-stu-id="f822c-200">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="f822c-201">Ve výchozím nastavení SignalR vrátí obecnou chybovou zprávu do klienta.</span><span class="sxs-lookup"><span data-stu-id="f822c-201">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="f822c-202">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f822c-202">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="f822c-203">Neočekávané výjimky často obsahují citlivé informace, jako je například název databáze serveru ve výjimce aktivuje v případě selhání připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="f822c-203">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="f822c-204">Funkce SignalR nezveřejňuje tyto podrobné chybové zprávy ve výchozím nastavení jako bezpečnostní opatření.</span><span class="sxs-lookup"><span data-stu-id="f822c-204">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="f822c-205">Zobrazit [článku aspekty zabezpečení](xref:signalr/security#exceptions) Další informace o důvod, proč jsou potlačeny podrobnosti o výjimce.</span><span class="sxs-lookup"><span data-stu-id="f822c-205">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="f822c-206">Pokud máte výjimečné podmínce můžete *proveďte* šířit do klienta, můžete použít `HubException` třídy.</span><span class="sxs-lookup"><span data-stu-id="f822c-206">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="f822c-207">Pokud vyvoláte `HubException` z vaší metody rozbočovače SignalR **bude** odeslat celá zpráva ke klientovi ponechat beze změny.</span><span class="sxs-lookup"><span data-stu-id="f822c-207">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="f822c-208">Funkce SignalR odesílá pouze `Message` vlastnosti výjimky do klienta.</span><span class="sxs-lookup"><span data-stu-id="f822c-208">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="f822c-209">Trasování zásobníku a dalších vlastností o výjimce nejsou k dispozici ke klientovi.</span><span class="sxs-lookup"><span data-stu-id="f822c-209">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f822c-210">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="f822c-210">Related resources</span></span>

* [<span data-ttu-id="f822c-211">Úvod do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="f822c-211">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="f822c-212">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="f822c-212">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="f822c-213">Publikování do Azure</span><span class="sxs-lookup"><span data-stu-id="f822c-213">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
