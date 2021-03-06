---
title: Správa uživatelů a skupin v nástroji SignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR správy uživatelů a skupin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: 0dfdf3a5eccd7462b675554e02fe4d2e166e8b92
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627556"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a>Správa uživatelů a skupin v nástroji SignalR

Od [Brennan Conroy](https://github.com/BrennanConroy)

SignalR umožňuje posílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli i k pojmenovaným skupinám připojení.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="users-in-no-locsignalr"></a>Uživatelé v SignalR

Jeden uživatel v nástroji SignalR může mít několik připojení k aplikaci. Například uživatel může být připojen na svém počítači a také na telefonu. Každé zařízení má samostatné SignalR připojení, ale všechny jsou přidružené ke stejnému uživateli. Pokud se uživateli pošle zpráva, zobrazí se tato zpráva u všech připojení přidružených k tomuto uživateli. K identifikátoru uživatele pro připojení lze použít `Context.UserIdentifier` vlastnost v centru.

Ve výchozím nastavení SignalR používá nástroj `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele. Chcete-li toto chování přizpůsobit, přečtěte si téma [použití deklarací k přizpůsobení manipulace s identitou](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Odeslat zprávu konkrétnímu uživateli předáním identifikátoru uživatele do `User` funkce v metodě centra, jak je znázorněno v následujícím příkladu:

> [!NOTE]
> Identifikátor uživatele rozlišuje velká a malá písmena.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a>Skupiny v SignalR

Skupina je kolekcí připojení přidružených k názvu. Zprávy je možné odesílat do všech připojení ve skupině. Skupiny jsou doporučeným způsobem, jak odeslat připojení nebo více připojení, protože skupiny jsou spravovány aplikací. Připojení může být členem více skupin. Skupiny jsou ideální pro něco podobného aplikaci Chat, kde každou místnost lze reprezentovat jako skupinu. Připojení se přidávají nebo odebírají ze skupin prostřednictvím `AddToGroupAsync` metod a `RemoveFromGroupAsync` .

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

Členství ve skupině se nezachová, když se připojení znovu připojí. Připojení se musí znovu připojit ke skupině, když je znovu navázána. Není možné spočítat členy skupiny, protože tyto informace nejsou k dispozici, pokud je aplikace škálovaná na více serverů.

K ochraně přístupu k prostředkům při používání skupin použijte funkci [ověřování a autorizace](xref:signalr/authn-and-authz) v ASP.NET Core. Pokud je uživatel přidán do skupiny pouze v případě, že jsou pověření platná pro tuto skupinu, zprávy odesílané do této skupiny budou přecházet pouze autorizovaným uživatelům. Skupiny ale nejsou funkcí zabezpečení. Deklarace identity ověřování mají funkce, které skupiny nemají, například vypršení platnosti a odvolání. Pokud je oprávnění uživatele k přístupu ke skupině odvoláno, aplikace musí explicitně odebrat uživatele ze skupiny.

> [!NOTE]
> V názvech skupin se rozlišují malá a velká písmena.

## <a name="related-resources"></a>Související prostředky

* [Začínáme](xref:tutorials/signalr)
* [Rozbočovače](xref:signalr/hubs)
* [Publikování do Azure](xref:signalr/publish-to-azure-web-app)
