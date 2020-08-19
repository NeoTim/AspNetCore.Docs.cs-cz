---
title: Možnosti ověřování komunity OSS pro ASP.NET Core
author: rick-anderson
description: Zjistí možnosti Open Source ověřování pro ASP.NET Core.
ms.author: riande
ms.date: 10/28/2019
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
uid: security/authentication/community
ms.openlocfilehash: ae95221850711b4133a0966c0bd787b892135698
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627166"
---
# <a name="community-oss-authentication-options-for-aspnet-core"></a><span data-ttu-id="a0a8e-103">Možnosti ověřování komunity OSS pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a0a8e-103">Community OSS authentication options for ASP.NET Core</span></span>

<span data-ttu-id="a0a8e-104">Tato stránka obsahuje otevřené možnosti ověřování zdrojového kódu pro ASP.NET Core poskytované komunitou.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-104">This page contains community-provided, open source authentication options for ASP.NET Core.</span></span> <span data-ttu-id="a0a8e-105">Tato stránka se pravidelně aktualizuje, protože budou k dispozici Noví zprostředkovatelé.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-105">This page is periodically updated as new providers become available.</span></span>

## <a name="oss-authentication-providers"></a><span data-ttu-id="a0a8e-106">Poskytovatelé ověřování OSS</span><span class="sxs-lookup"><span data-stu-id="a0a8e-106">OSS authentication providers</span></span>

<span data-ttu-id="a0a8e-107">Níže uvedený seznam je seřazený podle abecedy.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-107">The list below is sorted alphabetically.</span></span>

| <span data-ttu-id="a0a8e-108">Název</span><span class="sxs-lookup"><span data-stu-id="a0a8e-108">Name</span></span> | <span data-ttu-id="a0a8e-109">Popis</span><span class="sxs-lookup"><span data-stu-id="a0a8e-109">Description</span></span> |
| ---- | ----------- |
| [<span data-ttu-id="a0a8e-110">AspNet. Security. OpenIdConnect. Server (ASOS)</span><span class="sxs-lookup"><span data-stu-id="a0a8e-110">AspNet.Security.OpenIdConnect.Server (ASOS)</span></span>](https://github.com/aspnet-contrib/AspNet.Security.OpenIdConnect.Server) | <span data-ttu-id="a0a8e-111">ASOS je serverová architektura nižší úrovně OpenID Connect serveru pro ASP.NET Core a OWIN/Katana.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-111">ASOS is a low-level, protocol-first OpenID Connect server framework for ASP.NET Core and OWIN/Katana.</span></span> |
| [<span data-ttu-id="a0a8e-112">Server Gluu</span><span class="sxs-lookup"><span data-stu-id="a0a8e-112">Gluu Server</span></span>](https://gluu.org/) | <span data-ttu-id="a0a8e-113">Enterprise readed, open source software pro identitu, správu přístupu (IAM) a jednotné přihlašování (SSO).</span><span class="sxs-lookup"><span data-stu-id="a0a8e-113">Enterprise ready, open source software for identity, access management (IAM), and single sign-on (SSO).</span></span> <span data-ttu-id="a0a8e-114">Další informace najdete v [dokumentaci k produktu Gluu](https://gluu.org/docs/).</span><span class="sxs-lookup"><span data-stu-id="a0a8e-114">For more information, see the [Gluu Product Documentation](https://gluu.org/docs/).</span></span> |
| [<span data-ttu-id="a0a8e-115">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="a0a8e-115">IdentityServer</span></span>](https://identityserver.io/) | <span data-ttu-id="a0a8e-116">IdentityServer je rozhraní OpenID Connect a OAuth 2,0 Framework pro ASP.NET Core oficiálně certifikováno OpenID Foundation a v rámci zásad správného řízení rozhraní .NET Foundation.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-116">IdentityServer is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core, officially certified by the OpenID Foundation and under governance of the .NET Foundation.</span></span> <span data-ttu-id="a0a8e-117">Další informace najdete v tématu [Vítá vás Identity Server4 (dokumentace)](https://identityserver4.readthedocs.io/en/latest/).</span><span class="sxs-lookup"><span data-stu-id="a0a8e-117">For more information, see [Welcome to IdentityServer4 (Documentation)](https://identityserver4.readthedocs.io/en/latest/).</span></span> |
| [<span data-ttu-id="a0a8e-118">OpenIddict</span><span class="sxs-lookup"><span data-stu-id="a0a8e-118">OpenIddict</span></span>](https://github.com/openiddict/openiddict-core) | <span data-ttu-id="a0a8e-119">OpenIddict je snadno použitelný Server OpenID Connect pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a0a8e-119">OpenIddict is an easy-to-use OpenID Connect server for ASP.NET Core.</span></span> |

<span data-ttu-id="a0a8e-120">Chcete-li přidat poskytovatele, [upravte tuto stránku](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span><span class="sxs-lookup"><span data-stu-id="a0a8e-120">To add a provider, [edit this page](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Faspnet%2FDocs%2Fedit%2Fmaster%2Faspnetcore%2Fsecurity%2Fauthentication%2Fcommunity.md).</span></span>
