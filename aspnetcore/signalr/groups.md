---
title: Správa uživatelů a skupin v knihovně SignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR uživatelů a skupin správy.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/groups
ms.openlocfilehash: 45f2bb44e03a586b7fc186525fdd3a2645c820d5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667749"
---
# <a name="manage-users-and-groups-in-signalr"></a>Správa uživatelů a skupin v knihovně SignalR

Podle [Brennan Conroy](https://github.com/BrennanConroy)

Funkce SignalR umožňuje zprávy k odeslání do všech připojení, které jsou spojené s konkrétním uživatelem, jakož i pojmenovaným skupinám připojení.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Uživatelé v knihovně SignalR

Funkce SignalR umožňuje odesílání zpráv do všech připojení, které jsou spojené s konkrétním uživatelem. Ve výchozím nastavení, používá SignalR `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidružené k připojení jako identifikátor uživatele. Jeden uživatel může mít více připojení k aplikaci s knihovnou SignalR. Například může být připojen uživatel na svém počítači, stejně jako svůj telefon. Každé zařízení má samostatného připojení SignalR, ale jsou všechny související se stejným uživatelem. Pokud je uživateli odeslána zpráva, všechna připojení přidružená k tomuto uživateli zobrazí zpráva. Identifikátor uživatele pro připojení je přístupný `Context.UserIdentifier` vlastnosti v centru.

Odeslání zprávy do konkrétního uživatele tím, že předáte identifikátor uživatele, který `User` fungovat ve své metodě rozbočovače, jak je znázorněno v následujícím příkladu:

> [!NOTE]
> Identifikátor uživatele je velká a malá písmena.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Skupinami v knihovně SignalR

Skupina je kolekce připojení přidružená k názvu. Pro všechna připojení ve skupině nelze odesílat zprávy. Skupiny jsou doporučeným způsobem, jak odesílat připojení nebo více připojení, protože tyto skupiny jsou spravována aplikací. Připojení může být členem více skupin. Díky tomu skupiny ideální pro něco jako je chatovací aplikaci, kde každý prostor může být reprezentována jako skupinu. Připojení může být přidán či odebrán ze skupiny přes `AddToGroupAsync` a `RemoveFromGroupAsync` metody.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Členství ve skupině se nezachová při opětovném navázání připojení. Připojení je potřeba znovu vstoupit skupině znovu zřízeno. Není možné počet členů skupiny, protože tyto informace není k dispozici v případě, že aplikace je škálovaný na více serverů.

Chcete-li chránit přístup k prostředkům a používání skupin, použijte [ověřování a autorizace](xref:signalr/authn-and-authz) funkce v ASP.NET Core. Pokud pouze přidáte uživatele do skupiny při přihlašovací údaje jsou platné pro tuto skupinu, zprávy odeslané do této skupiny přejdete jenom na autorizované uživatele. Skupiny však nejsou funkce zabezpečení. Ověřování deklarací identity má funkce, které skupiny tomu tak není, jako je například vypršení platnosti a odvolání. Pokud se odvolat oprávnění uživatele pro přístup ke skupině, budete muset ručně zjišťovat a odebrat ze skupiny.

> [!NOTE]
> Názvy skupin rozlišují malá a velká písmena.

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
