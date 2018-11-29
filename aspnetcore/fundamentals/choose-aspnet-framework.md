---
title: Zvolte mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje, ASP.NET Core vs. ASP.NET 4.x a jak si vybrat mezi nimi.
ms.author: riande
ms.date: 09/11/2018
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: fa281a068dfdcd8a7c45dd5d0a0c3084c4bc8dbc
ms.sourcegitcommit: e8d80ff566bfe505b43389d7bc4551edb1c0c872
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52549069"
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
|Vývoj s využitím sady Visual Studio [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/), nebo [Visual Studio Code](https://code.visualstudio.com/) pomocí C# neboF#|Vývoj s využitím sady Visual Studio C#, VB, neboF#|
|Vyšší výkon než ASP.NET 4.x|Dobrý výkon|
|[Zvolte rozhraní .NET Framework nebo .NET Core runtime](/dotnet/articles/standard/choosing-core-framework-server)|Použít modul runtime rozhraní .NET Framework|

Zobrazit [ASP.NET Core, které cílí na rozhraní .NET Framework](xref:index#target-framework) informace o podpoře ASP.NET Core 2.x na rozhraní .NET Framework.

## <a name="aspnet-core-scenarios"></a>Scénáře ASP.NET Core

* [Stránky Razor](xref:razor-pages/index) je doporučený postup pro vytváření webového uživatelského rozhraní k ASP.NET Core 2.x.
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

> [!NOTE]
> Testujeme použitelnost navrhované nové struktury pro obsah ASP.NET Core.  Pokud máte pár minut a chcete si vyzkoušet cvičení, které spočívá ve vyhledání 7 různých témat v aktuálním nebo navrhovaném obsahu, [klikněte prosím sem a zúčastněte se studie](https://dpk4xbh5.optimalworkshop.com/treejack/rps16hd5).