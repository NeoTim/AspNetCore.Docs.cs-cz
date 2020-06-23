---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243223"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor podporované platformy

Od [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Požadavky na prohlížeč

### <a name="blazor-webassembly"></a>BlazorWebAssembly

| Prohlížeč                          | Verze               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, včetně Androidu | Current               |
| Safari, včetně iOS            | Current               |
| Microsoft Internet Explorer      | Nepodporováno&dagger; |

&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>BlazorWebServer

| Prohlížeč                          | Verze    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, včetně Androidu | Current    |
| Safari, včetně iOS            | Current    |
| Microsoft Internet Explorer      | odst&dagger; |

&dagger;Jsou vyžadovány další obslužné nabídky (například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/hosting-models>
