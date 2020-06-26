---
title: Správa uživatelů a skupin v nástrojiSignalR
author: bradygaster
description: Přehled ASP.NET Core SignalR správy uživatelů a skupin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: af76402d02ee4273deadac246f275c1ae7ad84ac
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408510"
---
# <a name="manage-users-and-groups-in-signalr"></a><span data-ttu-id="2b121-103">Správa uživatelů a skupin v nástrojiSignalR</span><span class="sxs-lookup"><span data-stu-id="2b121-103">Manage users and groups in SignalR</span></span>

<span data-ttu-id="2b121-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="2b121-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

SignalR<span data-ttu-id="2b121-105">umožňuje posílat zprávy na všechna připojení přidružená ke konkrétnímu uživateli i k pojmenovaným skupinám připojení.</span><span class="sxs-lookup"><span data-stu-id="2b121-105"> allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="2b121-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2b121-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-signalr"></a><span data-ttu-id="2b121-107">Uživatelé vSignalR</span><span class="sxs-lookup"><span data-stu-id="2b121-107">Users in SignalR</span></span>

<span data-ttu-id="2b121-108">Jeden uživatel v nástroji SignalR může mít několik připojení k aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2b121-108">A single user in SignalR can have multiple connections to an app.</span></span> <span data-ttu-id="2b121-109">Například uživatel může být připojen na svém počítači a také na telefonu.</span><span class="sxs-lookup"><span data-stu-id="2b121-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="2b121-110">Každé zařízení má samostatné SignalR připojení, ale všechny jsou přidružené ke stejnému uživateli.</span><span class="sxs-lookup"><span data-stu-id="2b121-110">Each device has a separate SignalR connection, but they're all associated with the same user.</span></span> <span data-ttu-id="2b121-111">Pokud se uživateli pošle zpráva, zobrazí se tato zpráva u všech připojení přidružených k tomuto uživateli.</span><span class="sxs-lookup"><span data-stu-id="2b121-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="2b121-112">K identifikátoru uživatele pro připojení lze použít `Context.UserIdentifier` vlastnost v centru.</span><span class="sxs-lookup"><span data-stu-id="2b121-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="2b121-113">Ve výchozím nastavení SignalR používá nástroj `ClaimTypes.NameIdentifier` z `ClaimsPrincipal` přidruženého k připojení jako identifikátor uživatele.</span><span class="sxs-lookup"><span data-stu-id="2b121-113">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="2b121-114">Chcete-li toto chování přizpůsobit, přečtěte si téma [použití deklarací k přizpůsobení manipulace s identitou](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="2b121-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="2b121-115">Odeslat zprávu konkrétnímu uživateli předáním identifikátoru uživatele do `User` funkce v metodě centra, jak je znázorněno v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="2b121-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="2b121-116">Identifikátor uživatele rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2b121-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a><span data-ttu-id="2b121-117">Skupiny vSignalR</span><span class="sxs-lookup"><span data-stu-id="2b121-117">Groups in SignalR</span></span>

<span data-ttu-id="2b121-118">Skupina je kolekcí připojení přidružených k názvu.</span><span class="sxs-lookup"><span data-stu-id="2b121-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="2b121-119">Zprávy je možné odesílat do všech připojení ve skupině.</span><span class="sxs-lookup"><span data-stu-id="2b121-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="2b121-120">Skupiny jsou doporučeným způsobem, jak odeslat připojení nebo více připojení, protože skupiny jsou spravovány aplikací.</span><span class="sxs-lookup"><span data-stu-id="2b121-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="2b121-121">Připojení může být členem více skupin.</span><span class="sxs-lookup"><span data-stu-id="2b121-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="2b121-122">Skupiny jsou ideální pro něco podobného aplikaci Chat, kde každou místnost lze reprezentovat jako skupinu.</span><span class="sxs-lookup"><span data-stu-id="2b121-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="2b121-123">Připojení se přidávají nebo odebírají ze skupin prostřednictvím `AddToGroupAsync` metod a `RemoveFromGroupAsync` .</span><span class="sxs-lookup"><span data-stu-id="2b121-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="2b121-124">Členství ve skupině se nezachová, když se připojení znovu připojí.</span><span class="sxs-lookup"><span data-stu-id="2b121-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="2b121-125">Připojení se musí znovu připojit ke skupině, když je znovu navázána.</span><span class="sxs-lookup"><span data-stu-id="2b121-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="2b121-126">Není možné spočítat členy skupiny, protože tyto informace nejsou k dispozici, pokud je aplikace škálovaná na více serverů.</span><span class="sxs-lookup"><span data-stu-id="2b121-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="2b121-127">K ochraně přístupu k prostředkům při používání skupin použijte funkci [ověřování a autorizace](xref:signalr/authn-and-authz) v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2b121-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="2b121-128">Pokud je uživatel přidán do skupiny pouze v případě, že jsou pověření platná pro tuto skupinu, zprávy odesílané do této skupiny budou přecházet pouze autorizovaným uživatelům.</span><span class="sxs-lookup"><span data-stu-id="2b121-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="2b121-129">Skupiny ale nejsou funkcí zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="2b121-129">However, groups are not a security feature.</span></span> <span data-ttu-id="2b121-130">Deklarace identity ověřování mají funkce, které skupiny nemají, například vypršení platnosti a odvolání.</span><span class="sxs-lookup"><span data-stu-id="2b121-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="2b121-131">Pokud je oprávnění uživatele k přístupu ke skupině odvoláno, aplikace musí explicitně odebrat uživatele ze skupiny.</span><span class="sxs-lookup"><span data-stu-id="2b121-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="2b121-132">V názvech skupin se rozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="2b121-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="2b121-133">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="2b121-133">Related resources</span></span>

* [<span data-ttu-id="2b121-134">Začínáme</span><span class="sxs-lookup"><span data-stu-id="2b121-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2b121-135">Centra</span><span class="sxs-lookup"><span data-stu-id="2b121-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="2b121-136">Publikování aplikací do Azure</span><span class="sxs-lookup"><span data-stu-id="2b121-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
