---
title: Použití Center v ASP.NET CoreSignalR
author: bradygaster
description: Naučte se používat centra v ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hubs
ms.openlocfilehash: 6ea8a8e9ffb6549a285f320eb0a4a2e5d218483a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775216"
---
# <a name="use-hubs-in-signalr-for-aspnet-core"></a>Používat centra v nástroji Signal pro ASP.NET Core

Od [Rachel Appel](https://twitter.com/rachelappel) a [Kevin Griffin](https://twitter.com/1kevgriff)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-signalr-hub"></a>Co je centrum signálů

Rozhraní API centra signalizace umožňuje volat metody v připojených klientech ze serveru. V kódu serveru definujete metody, které jsou volány klientem. V kódu klienta definujete metody, které jsou volány ze serveru. Signál se stará o vše na pozadí, které umožňuje komunikaci mezi klientem a serverem od klientů po straně klienta.

## <a name="configure-signalr-hubs"></a>Konfigurovat centra signalizace

Middleware signálu vyžaduje některé služby, které jsou nakonfigurované voláním `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Když přidáváte funkci signalizace do aplikace ASP.NET Core, nastaví trasy signalizace volání `endpoint.MapHub` ve `Startup.Configure` `app.UseEndpoints` zpětném volání metody.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Při přidávání funkce signalizace do aplikace ASP.NET Core instalační program směruje trasy voláním `app.UseSignalR` `Startup.Configure` metody.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Vytváření a používání Center

Vytvořte centrum deklarováním třídy, která dědí z `Hub`a přidejte do ní veřejné metody. Klienti mohou volat metody, které jsou definovány `public`jako.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Můžete zadat návratový typ a parametry, včetně složitých typů a polí, stejně jako v libovolné metodě jazyka C#. Signal zpracovává serializaci a deserializaci komplexních objektů a polí ve vašich parametrech a návratových hodnotách.

> [!NOTE]
> Rozbočovače jsou přechodné:
>
> * Neukládat stav ve vlastnosti ve třídě hub. Každé volání metody centra je spuštěno na nové instanci centra.
> * Použijte `await` při volání asynchronních metod, které jsou závislé na době zachování rozbočovače. Například metoda, jako například, `Clients.All.SendAsync(...)` může selhat, pokud je volána bez `await` a metoda centra se dokončí před `SendAsync` dokončením.

## <a name="the-context-object"></a>Objekt kontextu

`Hub` Třída má `Context` vlastnost, která obsahuje následující vlastnosti s informacemi o připojení:

| Vlastnost | Popis |
| ------ | ----------- |
| `ConnectionId` | Získá jedinečné ID pro připojení, které přiřadí signál. Pro každé připojení existuje jedno ID připojení.|
| `UserIdentifier` | Získá [identifikátor uživatele](xref:signalr/groups). Ve výchozím nastavení signál používá `ClaimTypes.NameIdentifier` od `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele. |
| `User` | Získá `ClaimsPrincipal` přidružený k aktuálnímu uživateli. |
| `Items` | Získá kolekci klíč/hodnota, která se dá použít ke sdílení dat v rámci rozsahu tohoto připojení. Data mohou být uložena v této kolekci a budou uchována pro připojení v různých voláních metod rozbočovače. |
| `Features` | Získá kolekci funkcí dostupných na připojení. Ve většině scénářů teď není tato kolekce potřebná, takže ještě není popsána podrobněji. |
| `ConnectionAborted` | Získá upozornění `CancellationToken` , když je připojení přerušeno. |

`Hub.Context`obsahuje také následující metody:

| Metoda | Popis |
| ------ | ----------- |
| `GetHttpContext` | Vrátí `HttpContext` pro připojení, nebo `null` Pokud připojení není přidruženo k požadavku HTTP. V případě připojení HTTP můžete použít tuto metodu k získání informací, jako jsou hlavičky HTTP a řetězce dotazů. |
| `Abort` | Přeruší připojení. |

## <a name="the-clients-object"></a>Objekt klienti

`Hub` Třída má `Clients` vlastnost, která obsahuje následující vlastnosti pro komunikaci mezi serverem a klientem:

| Vlastnost | Popis |
| ------ | ----------- |
| `All` | Volá metodu na všech připojených klientech. |
| `Caller` | Volá metodu na klientovi, který vyvolal metodu hub. |
| `Others` | Volá metodu na všech připojených klientech s výjimkou klienta, který metodu vyvolal. |

`Hub.Clients`obsahuje také následující metody:

| Metoda | Popis |
| ------ | ----------- |
| `AllExcept` | Volá metodu na všech připojených klientech s výjimkou zadaných připojení. |
| `Client` | Volá metodu pro určitého připojeného klienta. |
| `Clients` | Volá metodu pro konkrétní připojené klienty. |
| `Group` | Volá metodu pro všechna připojení v zadané skupině.  |
| `GroupExcept` | Volá metodu pro všechna připojení v zadané skupině s výjimkou zadaných připojení. |
| `Groups` | Volá metodu pro více skupin připojení.  |
| `OthersInGroup` | Volá metodu pro skupinu připojení s výjimkou klienta, který vyvolal metodu hub.  |
| `User` | Volá metodu pro všechna připojení přidružená ke konkrétnímu uživateli. |
| `Users` | Volá metodu pro všechna připojení přidružená k určeným uživatelům. |

Každá vlastnost nebo metoda v předchozích tabulkách vrátí objekt s `SendAsync` metodou. `SendAsync` Metoda umožňuje zadejte název a parametry metody klienta, které mají být volány.

## <a name="send-messages-to-clients"></a>Odesílání zpráv klientům

Chcete-li volat konkrétní klienty, použijte vlastnosti `Clients` objektu. V následujícím příkladu jsou tři metody centra:

* `SendMessage`odešle zprávu všem připojeným klientům pomocí `Clients.All`.
* `SendMessageToCaller`pošle zprávu zpět volajícímu pomocí `Clients.Caller`.
* `SendMessageToGroups`pošle zprávu všem klientům ve `SignalR Users` skupině.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Rozbočovače silného typu

Nevýhodou použití `SendAsync` je, že při určení metody klienta, která má být volána, spoléhá na řetězec Magic. Tím zůstane kód otevřený pro běhové chyby, pokud je název metody špatně napsaný nebo chybí v klientovi.

Alternativa k použití `SendAsync` je silného typu `Hub` s <xref:Microsoft.AspNetCore.SignalR.Hub%601>. V následujícím příkladu byly `ChatHub` klientské metody extrahovány do rozhraní s názvem. `IChatClient`

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Toto rozhraní lze použít k refaktorování předchozího `ChatHub` příkladu.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Pomocí `Hub<IChatClient>` této metody lze provádět kontrolu klientských metod v době kompilace. To brání problémům způsobeným použitím řetězců Magic, protože `Hub<T>` může poskytnout přístup pouze k metodám definovaným v rozhraní.

Použití silného typu `Hub<T>` zakáže schopnost použít `SendAsync`. Jakékoli metody definované v rozhraní lze i nadále definovat jako asynchronní. Každá z těchto metod by ve skutečnosti měla vrátit `Task`. Vzhledem k tomu, že se jedná o rozhraní `async` , nepoužívejte klíčové slovo. Příklad:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> `Async` Přípona není z názvu metody odstraněna. Pokud vaše metoda klienta není definovaná pomocí `.on('MyMethodAsync')`, neměli byste používat `MyMethodAsync` jako název.

## <a name="change-the-name-of-a-hub-method"></a>Změna názvu metody centra

Ve výchozím nastavení je název metody centra serveru název metody .NET. Můžete však použít atribut [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) ke změně tohoto výchozího nastavení a ručně zadat název metody. Klient by měl použít tento název namísto názvu metody .NET při volání metody.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Zpracování událostí pro připojení

Rozhraní SignalR API Center poskytuje `OnConnectedAsync` virtuálním `OnDisconnectedAsync` metodám a ke správě a sledování připojení. Přepište `OnConnectedAsync` virtuální metodu tak, aby prováděla akce, když se klient připojí k rozbočovači, jako je například přidání do skupiny.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Přepište `OnDisconnectedAsync` virtuální metodu tak, aby prováděla akce, když se klient odpojí. Pokud se klient odpojí úmyslně (například voláním metody `connection.stop()`), bude `exception` parametr `null`. Pokud je však klient odpojen z důvodu chyby (například selhání sítě), `exception` parametr bude obsahovat výjimku popisující selhání.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Ošetření chyb

Výjimky vyvolané ve vašich metodách centra jsou odesílány klientovi, který tuto metodu vyvolal. V klientovi jazyka JavaScript vrátí `invoke` metoda [příslib JavaScriptu](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Když klient obdrží chybu s obslužnou rutinou připojenou k Promise pomocí `catch`, je vyvolána a předána jako objekt JavaScriptu `Error` .

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Pokud vaše centrum vyvolá výjimku, připojení se nezavřou. Ve výchozím nastavení SignalR vrátí klientovi obecnou chybovou zprávu. Příklad:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Neočekávané výjimky často obsahují citlivé informace, jako je třeba název databázového serveru v výjimce aktivované při neúspěchu připojení k databázi. SignalRnezveřejňuje tyto podrobné chybové zprávy ve výchozím nastavení jako bezpečnostní opatření. Další informace o tom, proč se potlačí podrobnosti o výjimce, najdete v článku věnovaném [bezpečnostním hlediskům](xref:signalr/security#exceptions) .

Pokud máte výjimečnou podmínku *, kterou chcete* rozšířit na klienta, můžete použít `HubException` třídu. Pokud vyvoláte `HubException` metodu z vašeho centra, SignalR **aplikace pošle** celou zprávu klientovi, který nezůstane beze změny.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalRpouze odesílá `Message` vlastnost výjimky klientovi. Trasování zásobníku a další vlastnosti výjimky nejsou pro klienta k dispozici.

## <a name="related-resources"></a>Související prostředky

* [Úvod k ASP.NET CoreSignalR](xref:signalr/introduction)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování aplikací do Azure](xref:signalr/publish-to-azure-web-app)
