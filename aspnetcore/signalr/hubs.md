---
title: Použití Center v ASP.NET Core SignalR
author: bradygaster
description: Naučte se používat centra v ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963803"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a>Použít centra v SignalR pro ASP.NET Core

Od [Rachel Appel](https://twitter.com/rachelappel) a [Kevin Griffin](https://twitter.com/1kevgriff)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-opno-locsignalr-hub"></a>Co je centrum SignalR

Rozhraní API centra SignalR umožňuje volat metody v připojených klientech ze serveru. V kódu serveru definujete metody, které jsou volány klientem. V kódu klienta definujete metody, které jsou volány ze serveru. SignalR postará o vše na pozadí, které umožňuje komunikaci mezi klientem a serverem v reálném čase.

## <a name="configure-opno-locsignalr-hubs"></a>Konfigurovat centra SignalR

Middleware SignalR vyžaduje některé služby, které jsou nakonfigurované voláním `services.AddSignalR`.

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Při přidávání SignalRch funkcí do aplikace ASP.NET Core instalační program SignalR trasy voláním `endpoint.MapHub` v `Startup.Configure`m zpětném volání metody `app.UseEndpoints`.

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Při přidávání SignalRch funkcí do aplikace ASP.NET Core instalační program SignalR trasy voláním `app.UseSignalR` v metodě `Startup.Configure`.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Vytváření a používání Center

Vytvořte centrum deklarováním třídy, která dědí z `Hub`a přidejte do ní veřejné metody. Klienti mohou volat metody, které jsou definovány jako `public`.

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Můžete zadat návratový typ a parametry, včetně složitých typů a polí, stejně jako v libovolné C# metodě. SignalR zpracovává serializaci a deserializaci komplexních objektů a polí ve vašich parametrech a návratových hodnotách.

> [!NOTE]
> Rozbočovače jsou přechodné:
>
> * Neukládat stav ve vlastnosti ve třídě hub. Každé volání metody centra je spuštěno na nové instanci centra.
> * Při volání asynchronních metod, které závisejí na době zachování rozbočovače, použijte `await`. Například metoda, například `Clients.All.SendAsync(...)`, může selhat, pokud je volána bez `await` a metoda centra se dokončí před `SendAsync` dokončení.

## <a name="the-context-object"></a>Objekt kontextu

Třída `Hub` obsahuje vlastnost `Context`, která obsahuje následující vlastnosti s informacemi o připojení:

| Vlastnost | Popis |
| ------ | ----------- |
| `ConnectionId` | Získá jedinečné ID pro připojení, které přiřadí SignalR. Pro každé připojení existuje jedno ID připojení.|
| `UserIdentifier` | Získá [identifikátor uživatele](xref:signalr/groups). Ve výchozím nastavení SignalR používá `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele. |
| `User` | Získá `ClaimsPrincipal` přidružený k aktuálnímu uživateli. |
| `Items` | Získá kolekci klíč/hodnota, která se dá použít ke sdílení dat v rámci rozsahu tohoto připojení. Data mohou být uložena v této kolekci a budou uchována pro připojení v různých voláních metod rozbočovače. |
| `Features` | Získá kolekci funkcí dostupných na připojení. Ve většině scénářů teď není tato kolekce potřebná, takže ještě není popsána podrobněji. |
| `ConnectionAborted` | Získá `CancellationToken`, který upozorní na přerušení připojení. |

`Hub.Context` také obsahuje následující metody:

| Metoda | Popis |
| ------ | ----------- |
| `GetHttpContext` | Vrátí `HttpContext` pro připojení, nebo `null`, pokud připojení není přidruženo k požadavku HTTP. V případě připojení HTTP můžete použít tuto metodu k získání informací, jako jsou hlavičky HTTP a řetězce dotazů. |
| `Abort` | Přeruší připojení. |

## <a name="the-clients-object"></a>Objekt klienti

Třída `Hub` obsahuje vlastnost `Clients`, která obsahuje následující vlastnosti pro komunikaci mezi serverem a klientem:

| Vlastnost | Popis |
| ------ | ----------- |
| `All` | Volá metodu na všech připojených klientech. |
| `Caller` | Volá metodu na klientovi, který vyvolal metodu hub. |
| `Others` | Volá metodu na všech připojených klientech s výjimkou klienta, který metodu vyvolal. |

`Hub.Clients` také obsahuje následující metody:

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

Každá vlastnost nebo metoda v předchozích tabulkách vrátí objekt s metodou `SendAsync`. Metoda `SendAsync` umožňuje zadejte název a parametry metody klienta, které mají být volány.

## <a name="send-messages-to-clients"></a>Odesílání zpráv klientům

Chcete-li volat konkrétní klienty, použijte vlastnosti objektu `Clients`. V následujícím příkladu jsou tři metody centra:

* `SendMessage` pošle zprávu všem připojeným klientům pomocí `Clients.All`.
* `SendMessageToCaller` pošle zprávu volajícímu pomocí `Clients.Caller`.
* `SendMessageToGroups` pošle zprávu všem klientům ve `SignalR Users` skupině.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Rozbočovače silného typu

Nevýhodou použití `SendAsync` je, že spoléhá na řetězec Magic, který určuje metodu klienta, která má být volána. Tím zůstane kód otevřený pro běhové chyby, pokud je název metody špatně napsaný nebo chybí v klientovi.

Alternativou k použití `SendAsync` je silného typu `Hub` s <xref:Microsoft.AspNetCore.SignalR.Hub%601>. V následujícím příkladu byly klientské metody `ChatHub` extrahovány do rozhraní s názvem `IChatClient`.

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Toto rozhraní se dá použít k refaktorování předchozího `ChatHub`ho příkladu.

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

Použití `Hub<IChatClient>` povolí kontrolu klientských metod v době kompilace. Tím zabráníte problémům způsobeným použitím řetězců Magic, protože `Hub<T>` může poskytnout přístup pouze k metodám definovaným v rozhraní.

Použití `Hub<T>` silného typu zakáže možnost použití `SendAsync`. Jakékoli metody definované v rozhraní lze i nadále definovat jako asynchronní. Každá z těchto metod by ve skutečnosti měla vracet `Task`. Vzhledem k tomu, že se jedná o rozhraní, nepoužívejte klíčové slovo `async`. Příklad:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> Přípona `Async` není z názvu metody odstraněna. Pokud není metoda klienta definovaná pomocí `.on('MyMethodAsync')`, neměli byste používat `MyMethodAsync` jako název.

## <a name="change-the-name-of-a-hub-method"></a>Změna názvu metody centra

Ve výchozím nastavení je název metody centra serveru název metody .NET. Můžete však použít atribut [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) ke změně tohoto výchozího nastavení a ručně zadat název metody. Klient by měl použít tento název namísto názvu metody .NET při volání metody.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Zpracování událostí pro připojení

Rozhraní API centra SignalR poskytuje virtuální metody `OnConnectedAsync` a `OnDisconnectedAsync` pro správu a sledování připojení. Přepište virtuální metodu `OnConnectedAsync`, aby prováděla akce, když se klient připojí k rozbočovači, jako je například přidání do skupiny.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Přepište virtuální metodu `OnDisconnectedAsync`, aby prováděla akce, když se klient odpojí. Pokud se klient odpojí úmyslně (například voláním `connection.stop()`), parametr `exception` bude `null`. Pokud je však klient odpojen z důvodu chyby (například selhání sítě), parametr `exception` bude obsahovat výjimku popisující selhání.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a>Ošetření chyb

Výjimky vyvolané ve vašich metodách centra jsou odesílány klientovi, který tuto metodu vyvolal. V klientu jazyka JavaScript vrátí metoda `invoke` [příslib JavaScriptu](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). Když klient obdrží chybu s obslužnou rutinou připojenou k Promise pomocí `catch`, je vyvolána a předána jako JavaScriptový objekt `Error`.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Pokud vaše centrum vyvolá výjimku, připojení se nezavřou. Ve výchozím nastavení SignalR vrátí klientovi obecnou chybovou zprávu. Příklad:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Neočekávané výjimky často obsahují citlivé informace, jako je třeba název databázového serveru v výjimce aktivované při neúspěchu připojení k databázi. SignalR nevystaví tyto podrobné chybové zprávy ve výchozím nastavení jako bezpečnostní opatření. Další informace o tom, proč se potlačí podrobnosti o výjimce, najdete v článku věnovaném [bezpečnostním hlediskům](xref:signalr/security#exceptions) .

Pokud máte výjimečnou podmínku *, kterou chcete* rozšířit na klienta, můžete použít třídu `HubException`. Pokud z metody rozbočovače vyvoláte `HubException`, SignalR pošle celou zprávu klientovi, **zůstane** beze změny.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR odesílá klientovi pouze vlastnost `Message` výjimky. Trasování zásobníku a další vlastnosti výjimky nejsou pro klienta k dispozici.

## <a name="related-resources"></a>Související prostředky

* [Úvod do ASP.NET Core SignalR](xref:signalr/introduction)
* [Klient JavaScriptu](xref:signalr/javascript-client)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
