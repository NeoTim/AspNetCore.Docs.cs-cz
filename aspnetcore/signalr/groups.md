---
title: Správa uživatelů a skupin v SignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR správy uživatelů a skupin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/groups
ms.openlocfilehash: 59e90042ecbaf936602643bbdc3965e036426b26
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963813"
---
# <a name="manage-users-and-groups-in-opno-locsignalr"></a>Správa uživatelů a skupin v SignalR

Od [Brennan Conroy](https://github.com/BrennanConroy)

SignalR umožňuje odesílání zpráv do všech připojení přidružených ke konkrétnímu uživateli a k pojmenovaným skupinám připojení.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="users-in-opno-locsignalr"></a>Uživatelé v SignalR

SignalR umožňuje odesílat zprávy všem připojením přidruženým ke konkrétnímu uživateli. Ve výchozím nastavení SignalR používá `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele. Jeden uživatel může mít několik připojení k aplikaci SignalR. Například uživatel může být připojen na svém počítači a také na telefonu. Každé zařízení má samostatné SignalR připojení, ale všechny jsou přidružené ke stejnému uživateli. Pokud se uživateli pošle zpráva, zobrazí se tato zpráva u všech připojení přidružených k tomuto uživateli. K identifikátoru uživatele pro připojení lze použít vlastnost `Context.UserIdentifier` ve vašem centru.

Odeslat zprávu konkrétnímu uživateli předáním identifikátoru uživatele do funkce `User` v metodě hub, jak je znázorněno v následujícím příkladu:

> [!NOTE]
> Identifikátor uživatele rozlišuje velká a malá písmena.

[!code-csharp[Configure service](groups/sample/hubs/chathub.cs?range=29-32)]

## <a name="groups-in-opno-locsignalr"></a>Skupiny v SignalR

Skupina je kolekcí připojení přidružených k názvu. Zprávy je možné odesílat do všech připojení ve skupině. Skupiny jsou doporučeným způsobem, jak odeslat připojení nebo více připojení, protože skupiny jsou spravovány aplikací. Připojení může být členem více skupin. Díky tomu jsou skupiny ideální pro něco podobného aplikaci Chat, kde každou místnost lze reprezentovat jako skupinu. Do skupin můžete přidat nebo odebrat připojení prostřednictvím `AddToGroupAsync` a `RemoveFromGroupAsync`ch metod.

[!code-csharp[Hub methods](groups/sample/hubs/chathub.cs?range=15-27)]

Členství ve skupině se nezachová, když se připojení znovu připojí. Připojení se musí znovu připojit ke skupině, když je znovu navázána. Není možné spočítat členy skupiny, protože tyto informace nejsou k dispozici, pokud je aplikace škálovaná na více serverů.

K ochraně přístupu k prostředkům při používání skupin použijte funkci [ověřování a autorizace](xref:signalr/authn-and-authz) v ASP.NET Core. Pokud přidáte uživatele do skupiny pouze v případě, že jsou pověření platná pro tuto skupinu, zprávy odeslané do této skupiny budou přecházet pouze autorizovaným uživatelům. Skupiny ale nejsou funkcí zabezpečení. Deklarace identity ověřování mají funkce, které skupiny nemají, například vypršení platnosti a odvolání. Pokud je oprávnění uživatele k přístupu ke skupině odvoláno, musíte je ručně zjistit a odebrat ze skupiny.

> [!NOTE]
> V názvech skupin se rozlišují malá a velká písmena.

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Centra](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
