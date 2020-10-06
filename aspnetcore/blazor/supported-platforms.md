---
title: ASP.NET Core Blazor podporované platformy
author: guardrex
description: Přečtěte si o podporovaných platformách pro ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754538"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor podporované platformy

Od [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly a Blazor Server jsou podporovány v prohlížečích, které jsou uvedeny v následující tabulce.

| Prohlížeč                          | Verze         |
| -------------------------------- | --------------- |
| Apple Safari, včetně iOS      | Aktivní&dagger; |
| Google Chrome, včetně Androidu | Aktivní&dagger; |
| Microsoft Edge                   | Aktivní&dagger; |
| Mozilla Firefox                  | Aktivní&dagger; |  

&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Prohlížeč                          | Verze               |
| -------------------------------- | --------------------- |
| Apple Safari, včetně iOS      | Aktivní&dagger;       |
| Google Chrome, včetně Androidu | Aktivní&dagger;       |
| Microsoft Edge                   | Aktivní&dagger;       |
| Microsoft Internet Explorer      | Nepodporováno&Dagger; |
| Mozilla Firefox                  | Aktivní&dagger;       |  

&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.  
&Dagger;Aplikace Microsoft Internet Explorer nepodporuje [WebAssembly](https://webassembly.org).

## Blazor Server

| Prohlížeč                          | Verze         |
| -------------------------------- | --------------- |
| Apple Safari, včetně iOS      | Aktivní&dagger; |
| Google Chrome, včetně Androidu | Aktivní&dagger; |
| Microsoft Edge                   | Aktivní&dagger; |
| Microsoft Internet Explorer      | odst&Dagger;      |
| Mozilla Firefox                  | Aktivní&dagger; |

&dagger;*Aktuální* odkazuje na nejnovější verzi prohlížeče.  
&Dagger;Další obslužné výplně jsou povinné. Například příslibů lze přidat prostřednictvím [`Polyfill.io`](https://polyfill.io/v3/) sady prostředků.

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
