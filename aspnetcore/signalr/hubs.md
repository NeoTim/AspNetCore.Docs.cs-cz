---
title: Použití rozbočovače signalr technologie ASP.NET Core
author: bradygaster
description: Další informace o použití rozbočovače signalr technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/20/2018
uid: signalr/hubs
ms.openlocfilehash: 9bc74079235338c75c47e06bde2b78dc1c466bd6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836685"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Použití rozbočovače signalr pro ASP.NET Core

Podle [Rachel Appel](https://twitter.com/rachelappel) a [Kevin Griffin](https://twitter.com/1kevgriff)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>Co je rozbočovače SignalR

Rozhraní API pro rozbočovače SignalR můžete volat metody pro připojené klienty ze serveru. V serverovém kódu definovat metody, které jsou volány klientem. V kódu klienta můžete definovat metody, které jsou volány ze serveru. Funkce SignalR se postará o všechno, co na pozadí, které umožňuje komunikaci klienta se serverem a server klient v reálném čase.

## <a name="configure-signalr-hubs"></a>Konfigurace rozbočovače SignalR

SignalR middleware vyžaduje některé služby, které jsou nakonfigurované pomocí volání `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

Při přidávání funkce SignalR pro aplikace ASP.NET Core, nastavit trasy SignalR voláním `app.UseSignalR` v `Startup.Configure` metody.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

## <a name="create-and-use-hubs"></a>Vytvoření a použití rozbočovače

Vytvoření centra deklarováním třídy, která dědí z `Hub`a přidejte do ní veřejné metody. Klienti mohou volat metody, které jsou definovány jako `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Můžete určit návratový typ a parametry, včetně komplexní typy a pole, stejně jako v jakékoli metodě jazyka C#. Funkce SignalR zpracovává serializace a deserializace komplexních objektů a polí v parametry a návratové hodnoty.

> [!NOTE]
> Centra jsou přechodné:
> * Neukládejte stav do vlastnosti u třídy rozbočovače. Každé volání metody rozbočovače, je proveden v nové instanci rozbočovače.  
> * Použití `await` při volání asynchronní metody, které jsou závislé na rozbočovači zůstává aktivní. Například metoda jako `Clients.All.SendAsync(...)` může selhat, pokud je volána bez `await` a metody rozbočovače nedokončí, před `SendAsync` dokončí.

## <a name="the-context-object"></a>Objekt kontextu

`Hub` Třída nemá `Context` vlastnost, která obsahuje následující vlastnosti s informacemi o připojení:

| Vlastnost | Popis |
| ------ | ----------- |
| `ConnectionId` | Získá jedinečný ID pro připojení, přiřazené systémem SignalR. Existuje jedno ID připojení pro každé připojení.|
| `UserIdentifier` | Získá [identifikátor uživatele](xref:signalr/groups). Ve výchozím nastavení, používá SignalR `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidružené k připojení jako identifikátor uživatele. |
| `User` | Získá `ClaimsPrincipal` spojené s aktuálním uživatelem. |
| `Items` | Získá kolekci klíč/hodnota, která umožňuje sdílet data v rámci oboru pro toto připojení. Data mohou být uložena v této kolekci a se zachová připojení napříč volání metod rozbočovače na jiný. |
| `Features` | Získá kolekci funkcí, které jsou k dispozici na připojení. Teď není potřeba tuto kolekci ve většině scénářů, takže ho není podrobně popsány v ještě. |
| `ConnectionAborted` | Získá `CancellationToken` , která upozorní, když je připojení přerušeno. |

`Hub.Context` obsahuje také následující metody:

| Metoda | Popis |
| ------ | ----------- |
| `GetHttpContext` | Vrátí `HttpContext` pro připojení, nebo `null` Pokud připojení není přidružený požadavku HTTP. Pro připojení prostřednictvím protokolu HTTP můžete použít tuto metodu se získat informace, jako jsou hlavičky protokolu HTTP a řetězce dotazu. |
| `Abort` | Zruší připojení. |

## <a name="the-clients-object"></a>Objekt klientů

`Hub` Třída nemá `Clients` vlastnost, která obsahuje následující vlastnosti pro komunikaci mezi serverem a klientem:

| Vlastnost | Popis |
| ------ | ----------- |
| `All` | Volá metodu na všechny připojené klienty |
| `Caller` | Volá metodu na straně klienta, který volal metodu rozbočovače na |
| `Others` | Volá metodu na všechny připojené klienty kromě klienta, který volal metodu |

`Hub.Clients` obsahuje také následující metody:

| Metoda | Popis |
| ------ | ----------- |
| `AllExcept` | Volá metodu na všechny připojené klienty s výjimkou zadaných připojení |
| `Client` | Volá metodu pro konkrétní připojení klienta |
| `Clients` | Volá metodu pro konkrétní připojených klientů |
| `Group` | Volá metodu pro všechna připojení v určené skupině  |
| `GroupExcept` | Volá metodu pro všechna připojení v určené skupině s výjimkou zadaných připojení |
| `Groups` | Volá metodu pro více skupin pro připojení  |
| `OthersInGroup` | Volá metodu pro připojení s výjimkou klienta, který volal metodu rozbočovače na skupinu  |
| `User` | Volá metodu pro všechna připojení, které jsou spojené s konkrétním uživatelem |
| `Users` | Volá metodu pro všechna připojení přidružená k určení uživatelé |

Každé vlastnosti nebo metody v předchozích tabulkách vrátí objekt s `SendAsync` metoda. `SendAsync` Metoda vám umožňuje zadat název a parametry metody volání.

## <a name="send-messages-to-clients"></a>Odesílání zpráv do klientů

Chcete-li provést volání do konkrétních klientů, použijte vlastnosti `Clients` objektu. V následujícím příkladu jsou tři metody rozbočovače:

* `SendMessage` Odešle zprávu do všech připojených klientů používajících `Clients.All`.
* `SendMessageToCaller` Odešle zprávu zpět do volajícího a pomocí `Clients.Caller`.
* `SendMessageToGroups` Odešle zprávu pro všechny klienty v `SignalR Users` skupiny.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Silného typu rozbočovače

Nevýhodou použití `SendAsync` je, že spoléhá na magický řetězec a určit metodu klienta k volání. Kvůli tomu open kód chyby za běhu, pokud název metody je zadáno chybně nebo chybějící z klienta.

O alternativu k použití `SendAsync` silného typu, je `Hub` s <xref:Microsoft.AspNetCore.SignalR.Hub`1>. V následujícím příkladu `ChatHub` metody klienta bylo extrahováno ven do rozhraní volá `IChatClient`.  

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Toto rozhraní je možné Refaktorovat předchozí `ChatHub` příklad.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Pomocí `Hub<IChatClient>` povolí kompilaci kontrolu metodu klienta. To brání problémy způsobené pomocí magic řetězců, protože `Hub<T>` můžete poskytnout přístup k metodám definované v rozhraní.

Pomocí silného typu `Hub<T>` zakáže možnost používat `SendAsync`. Jakékoli metody definované v rozhraní může být stále definována jako o asynchronním. Ve skutečnosti, každá z těchto metod by mělo vrátit `Task`. Protože je rozhraní, nepoužívejte `async` – klíčové slovo. Příklad:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async` Přípona není odebrána z názvu metody. Pokud vaše metoda klienta je definována s `.on('MyMethodAsync')`, neměli byste používat `MyMethodAsync` jako název.

## <a name="change-the-name-of-a-hub-method"></a>Změnit název metody rozbočovače

Výchozí název metody rozbočovače serveru je název metody rozhraní .NET. Můžete však použít [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) změnit toto výchozí nastavení a ručně zadávat název metody atributu. Klient musí použít tento název místo názvu metody rozhraní .NET, při volání metody.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Zpracování událostí pro připojení

Poskytuje rozhraní API pro rozbočovače SignalR `OnConnectedAsync` a `OnDisconnectedAsync` virtuální metody pro správu a sledování připojení. Přepsat `OnConnectedAsync` virtuální metody pro provádění akcí po připojení klienta k rozbočovači, jako je například přidávání do skupiny.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Přepsat `OnDisconnectedAsync` virtuální metody pro provádění akcí po odpojení klienta. Pokud se klient odpojí záměrně (voláním `connection.stop()`, například), `exception` parametr bude `null`. Nicméně, pokud je klient odpojen z důvodu chyby (jako je například selhání sítě), `exception` parametr bude obsahovat výjimku popisující chybu.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Zpracování chyb

Výjimky vzniklé v metodách vašeho centra se posílají klientovi, který volal metodu. Na klientovi JavaScript `invoke` metoda vrátí hodnotu [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Když klient obdrží chybu s obslužnou rutinou k němu připojit pomocí promise `catch`, ji má a předají jako JavaScript `Error` objektu.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Pokud vaše Centrum vyvolá výjimku, nebyly uzavřeny připojení. Ve výchozím nastavení SignalR vrátí obecnou chybovou zprávu do klienta. Příklad:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Neočekávané výjimky často obsahují citlivé informace, jako je například název databáze serveru ve výjimce aktivuje v případě selhání připojení k databázi. Funkce SignalR nezveřejňuje tyto podrobné chybové zprávy ve výchozím nastavení jako bezpečnostní opatření. Zobrazit [článku aspekty zabezpečení](xref:signalr/security#exceptions) Další informace o důvod, proč jsou potlačeny podrobnosti o výjimce.

Pokud máte výjimečné podmínce můžete *proveďte* šířit do klienta, můžete použít `HubException` třídy. Pokud vyvoláte `HubException` z vaší metody rozbočovače SignalR **bude** odeslat celá zpráva ke klientovi ponechat beze změny.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> Funkce SignalR odesílá pouze `Message` vlastnosti výjimky do klienta. Trasování zásobníku a dalších vlastností o výjimce nejsou k dispozici ke klientovi.

## <a name="related-resources"></a>Související prostředky

* [Úvod do ASP.NET Core SignalR](xref:signalr/introduction)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
