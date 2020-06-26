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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401932"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor podporované platformy

Od [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Požadavky na prohlížeč

### Blazor WebAssembly

| Prohlížeč                          | Verze               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, včetně Androidu | Current               |
| Safari, včetně iOS            | Current               |
| Microsoft Internet Explorer      | Nepodporováno&dagger; |

&dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).

### Blazor Server

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
