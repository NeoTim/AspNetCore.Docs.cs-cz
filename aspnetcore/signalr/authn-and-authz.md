---
title: Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core
author: tdykstra
description: Další informace o použití ověřování a autorizace v knihovně SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 06/29/2018
uid: signalr/authn-and-authz
ms.openlocfilehash: 6624b2b4361eb0a5bb6e45108878ee6fe5ecb443
ms.sourcegitcommit: b34b25da2ab68e6495b2460ff570468f16a9bf0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284410"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Ověřování a autorizace v knihovně SignalR technologie ASP.NET Core

Podle [Andrew Stanton sestry](https://twitter.com/anurse)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Ověření uživatelé připojení k rozbočovači SignalR

SignalR je možné s [ověřování ASP.NET Core](xref:security/authentication/identity) Chcete-li přidružit uživatele ke každé připojení. V rozbočovači, ověřovacích dat je přístupný z [ `HubConnectionContext.User` ](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) vlastnost. Ověřování umožňuje IOT hub a volat metody pro všechna připojení, které jsou spojeny s konkrétním uživatelem (viz [spravovat uživatele a skupiny v knihovně SignalR](xref:signalr/groups) Další informace). Několik připojení může být spojen s jedním uživatelem.

### <a name="cookie-authentication"></a>Ověřování souborů cookie

V aplikaci založené na prohlížeči umožňuje ověřování souborů cookie své stávající přihlašovací údaje k automaticky směrovat do připojení SignalR. Při používání prohlížeče klienta, je potřeba žádná další konfigurace. Pokud je uživatel přihlášen do vaší aplikace, připojení SignalR automaticky zdědí toto ověřování.

Soubory cookie jsou specifické pro prohlížeč tak, aby odesílal přístupové tokeny, ale můžete jim poslat neprohlížečových klientů. Při použití [klienta .NET](xref:signalr/dotnet-client), `Cookies` vlastnost lze nastavit v `.WithUrl` volání s cílem poskytnout soubor cookie. Ověřování souborem cookie z klienta .NET však vyžaduje aplikaci poskytují rozhraní API pro výměnu ověřovacích dat pro soubor cookie.

### <a name="bearer-token-authentication"></a>Ověřování tokenu nosiče

Klient může poskytnout přístupový token namísto použití do souboru cookie. Server ověří token a použije ho k identifikaci uživatele. Toto ověření se provádí pouze v případě, že připojení existuje. Během existence připojení serveru nebude znovu ověřit automaticky vyhledat token zrušení.

Na serveru, je nakonfigurován pomocí ověřování tokenu nosiče [middleware nosiče JWT](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

V klientovi JavaScript token, který se dá zadat pomocí [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) možnost.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

V klientovi .NET je podobná [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) vlastnost, která je možné nakonfigurovat token:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Funkce tokenu přístupu zadáte je volána před provedením **každý** HTTP žádost SignalR. Pokud potřebujete k obnovení tokenu, aby bylo možné udržovat aktivní připojení (protože to může vypršet jejich platnost během připojení), tak učinit z v rámci této funkce a vrátí aktualizovaný token.

Standardní webové rozhraní API se odešlou nosné tokeny v hlavičce HTTP. SignalR je však nelze nastavit tyto hlavičky v prohlížečích, při použití některých přenosů. Při použití Server-Sent události a protokoly Websocket, se přenášejí token jako parametr řetězce dotazu. Aby bylo možné na serveru z toho důvodu je vyžadována další konfigurace:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a>Soubory cookie vs. nosné tokeny 

Protože soubory cookie jsou specifické pro prohlížeče, odesílání z jiných typů klientů zvyšuje složitost ve srovnání s odesíláním nosné tokeny. Z tohoto důvodu není ověřování souborů cookie nedoporučuje, pokud aplikace potřebuje pouze k ověření uživatelů z prohlížeče klienta. Ověřování tokenu nosiče je doporučený postup při používání klientů kromě prohlížeče klienta.

### <a name="windows-authentication"></a>Ověřování systému Windows

Pokud [ověřování Windows](xref:security/authentication/windowsauth) je nakonfigurovaná funkce SignalR ve vaší aplikaci, můžete použít tuto identitu zabezpečit rozbočovače. Však aby bylo možné odesílat zprávy pro jednotlivé uživatele, je nutné přidat vlastní uživatelské ID zprostředkovatele. Je to proto, že ověřování systému Windows neposkytuje "Identifikátor názvu" deklarace identity, která používá funkci SignalR k určení uživatelské jméno.

Přidejte novou třídu, která implementuje `IUserIdProvider` a načítat je jedna z deklarací z uživatele, který chcete použít jako identifikátor. Chcete-li například použít deklarace identity "Name" (což je uživatelské jméno Windows ve formuláři `[Domain]\[Username]`), vytvořte následující třídy:

```csharp
public class NameUserIdProvider : IUserIdProvider
{
    public string GetUserId(HubConnectionContext connection)
    {
        return connection.User?.FindFirst(ClaimTypes.Name)?.Value;
    }
}
```

Spíše než `ClaimTypes.Name`, můžete použít libovolnou hodnotu od `User` (jako je například identifikátoru Windows SID atd.).

> [!NOTE]
> Hodnota, kterou zvolíte, musí být jedinečný mezi všemi uživateli ve vašem systému. Zpráva určená pro jednoho uživatele, jinak můžou být nakonec přejdete na jiného uživatele.

Zaregistrujte tuto součást v vaše `Startup.ConfigureServices` metoda **po** volání `.AddSignalR`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

V rozhraní .NET pro klienta, musí být povoleno ověřování Windows tak, že nastavíte [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) vlastnost:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Ověřování Windows je podporována pouze podle klientského prohlížeče, pokud používáte Microsoft Internet Explorer nebo Microsoft Edge.

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Povolit uživatelům přístup rozbočovače a metody

Ve výchozím nastavení může být volán všechny metody v rozbočovači neověřený uživatel. Abyste mohli vyžadovat ověřování, použijte [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) atribut k rozbočovači:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Můžete použít argumenty konstruktoru a vlastnosti `[Authorize]` atribut omezují přístup pouze uživatelům odpovídající konkrétní [zásad autorizace](xref:security/authorization/policies). Například, pokud máte vlastní zásady autorizace volá `MyAuthorizationPolicy` můžete zajistit, že přístup pouze uživatelé odpovídající této zásadě centra pomocí následujícího kódu:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

Může mít metod rozbočovače na jednotlivé `[Authorize]` i atribut. Pokud má aktuální uživatel neodpovídá zásady použité na metodu, volající vrátí chybu:

```csharp
[Authorize]
public class ChatHub: Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

## <a name="additional-resources"></a>Další zdroje

* [Ověřování tokenu nosiče v ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
