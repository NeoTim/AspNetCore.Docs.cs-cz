---
title: Správa uživatelů a skupin v nástrojiSignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR správy uživatelů a skupin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af498575899fcfa407aba9f9f49c0bfeabadc7a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776295"
---
# <a name="manage-users-and-groups-in-signalr"></a>Správa uživatelů a skupin v nástrojiSignalR

Od [Brennan Conroy](https://github.com/BrennanConroy)

SignalRumožňuje posílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli i k pojmenovaným skupinám připojení.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Uživatelé vSignalR

SignalRumožňuje odesílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli. Ve výchozím nastavení SignalR používá nástroj `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele. Jeden uživatel může mít několik připojení k SignalR aplikaci. Například uživatel může být připojen na svém počítači a také na telefonu. Každé zařízení má samostatné SignalR připojení, ale všechny jsou přidružené ke stejnému uživateli. Pokud se uživateli pošle zpráva, zobrazí se tato zpráva u všech připojení přidružených k tomuto uživateli. K identifikátoru uživatele pro připojení se dá použít `Context.UserIdentifier` vlastnost ve vašem centru.

Odeslat zprávu konkrétnímu uživateli předáním identifikátoru uživatele do `User` funkce v metodě centra, jak je znázorněno v následujícím příkladu:

> [!NOTE]
> Identifikátor uživatele rozlišuje velká a malá písmena.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Skupiny vSignalR

Skupina je kolekcí připojení přidružených k názvu. Zprávy je možné odesílat do všech připojení ve skupině. Skupiny jsou doporučeným způsobem, jak odeslat připojení nebo více připojení, protože skupiny jsou spravovány aplikací. Připojení může být členem více skupin. Díky tomu jsou skupiny ideální pro něco podobného aplikaci Chat, kde každou místnost lze reprezentovat jako skupinu. Připojení můžete přidat nebo odebrat ze skupin prostřednictvím metod `AddToGroupAsync` a. `RemoveFromGroupAsync`

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Členství ve skupině se nezachová, když se připojení znovu připojí. Připojení se musí znovu připojit ke skupině, když je znovu navázána. Není možné spočítat členy skupiny, protože tyto informace nejsou k dispozici, pokud je aplikace škálovaná na více serverů.

K ochraně přístupu k prostředkům při používání skupin použijte funkci [ověřování a autorizace](xref:signalr/authn-and-authz) v ASP.NET Core. Pokud přidáte uživatele do skupiny pouze v případě, že jsou pověření platná pro tuto skupinu, zprávy odeslané do této skupiny budou přecházet pouze autorizovaným uživatelům. Skupiny ale nejsou funkcí zabezpečení. Deklarace identity ověřování mají funkce, které skupiny nemají, například vypršení platnosti a odvolání. Pokud je oprávnění uživatele k přístupu ke skupině odvoláno, musíte je ručně zjistit a odebrat ze skupiny.

> [!NOTE]
> V názvech skupin se rozlišují malá a velká písmena.

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování aplikací do Azure](xref:signalr/publish-to-azure-web-app)
