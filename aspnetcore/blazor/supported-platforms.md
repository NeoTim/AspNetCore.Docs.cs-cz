---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Další informace o podporovaných platforem pro ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/21/2019
uid: blazor/supported-platforms
ms.openlocfilehash: 12ef3885044cfca17c2e7ffdb248fdebef26c48a
ms.sourcegitcommit: e67356f5e643a5d43f6d567c5c998ce6002bdeb4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005345"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor podporované platformy

Podle [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Požadavky na prohlížeč

### <a name="blazor-client-side"></a>Blazor na straně klienta

| Prohlížeč                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | aktuální               |
| Mozilla Firefox                  | aktuální               |
| Google Chrome, včetně Androidu | aktuální               |
| Safari, včetně systému iOS            | aktuální               |
| Microsoft Internet Explorer      | Nepodporuje se&dagger; |

&dagger;Microsoft Internet Explorer nepodporuje [WebAssembly](http://webassembly.org).

Další informace na model hostingu Blazor na straně klienta najdete v tématu <xref:blazor/hosting-models#client-side>.

### <a name="blazor-server-side"></a>Blazor na straně serveru

| Prohlížeč                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | aktuální    |
| Mozilla Firefox                  | aktuální    |
| Google Chrome, včetně Androidu | aktuální    |
| Safari, včetně systému iOS            | aktuální    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Jsou vyžadovány další polyfills (například příslibů jde přidat prostřednictvím [Polyfill.io](https://polyfill.io/v3/) sady).

Další informace na model hostingu Blazor na straně serveru, naleznete v tématu <xref:blazor/hosting-models#server-side>.
