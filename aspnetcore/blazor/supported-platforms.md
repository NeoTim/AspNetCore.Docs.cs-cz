---
title: ASP.NET Blazor platformy podporované core
author: guardrex
description: Další informace o podporovaných Blazorplatformách pro ASP.NET Core .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658856"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>platformy podporované ASP.NET Core Blazor

Podle [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Požadavky na prohlížeč

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Prohlížeč                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, včetně Androidu | Current               |
| Safari, včetně iOS            | Current               |
| Microsoft Internet Explorer      | Nepodporuje se&dagger; |

&dagger;Aplikace Microsoft Internet Explorer nepodporuje [webovou sestavu](https://webassembly.org).

### <a name="blazor-server"></a>Blazor Server

| Prohlížeč                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, včetně Androidu | Current    |
| Safari, včetně iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Jsou vyžadovány další polyfilly (například sliby lze přidat prostřednictvím [Polyfill.io](https://polyfill.io/v3/) svazku).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/hosting-models>
