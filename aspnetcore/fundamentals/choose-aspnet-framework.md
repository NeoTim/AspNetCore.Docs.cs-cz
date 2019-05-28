---
title: Zvolte mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje, ASP.NET Core vs. ASP.NET 4.x a jak si vybrat mezi nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 05/02/2019
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a51d9946c9e65bd1665c610153f724c6087c9f7f
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251367"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Zvolte mezi ASP.NET 4.x a ASP.NET Core

ASP.NET Core je verzí ASP.NET 4.x. V tomto článku jsou uvedeny rozdíly mezi nimi.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core je open source, napříč platformami platforma pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linuxu.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x je vyspělá architektura, která poskytuje služby nezbytné k sestavení na podnikové úrovni, server webových aplikací založených na Windows.

## <a name="framework-selection"></a>Výběr rozhraní Framework

Následující tabulka porovnává ASP.NET Core, ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Sestavení pro Windows, macOS nebo Linux|Sestavení pro Windows|
|[Stránky Razor](xref:razor-pages/index) je doporučený postup pro vytváření webového uživatelského rozhraní k ASP.NET Core 2.x. Viz také [MVC](xref:mvc/overview), [webové rozhraní API](xref:tutorials/first-web-api), a [SignalR](xref:signalr/introduction).|Použití [webových formulářů](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [webové rozhraní API](/aspnet/web-api/), [Webhooky](/aspnet/webhooks/), nebo [webové stránky](/aspnet/web-pages)|
|Více verzí na počítač|Jedna verze na počítač|
|Vývoj s využitím sady Visual Studio [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# neboF#|Vývoj s využitím sady Visual Studio C#, VB, neboF#|
|Vyšší výkon než ASP.NET 4.x|Dobrý výkon|
|[Zvolte rozhraní .NET Framework nebo .NET Core runtime](/dotnet/standard/choosing-core-framework-server)|Použít modul runtime rozhraní .NET Framework|

Zobrazit [ASP.NET Core, které cílí na rozhraní .NET Framework](xref:index#target-framework) informace o podpoře ASP.NET Core 2.x na rozhraní .NET Framework.

## <a name="aspnet-core-scenarios"></a>Scénáře ASP.NET Core

* [Weby](xref:tutorials/first-mvc-app/index)
* [Rozhraní API](xref:tutorials/first-web-api)
* [V reálném čase](xref:signalr/index)
* [Nasazení aplikace ASP.NET Core do Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x scénáře

* [Weby](/aspnet/mvc)
* [Rozhraní API](/aspnet/web-api)
* [V reálném čase](/aspnet/signalr)
* [Vytvoření webové aplikace ASP.NET 4.x v Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Další zdroje

* [Úvod do ASP.NET](/aspnet/overview)
* [Úvod do ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
