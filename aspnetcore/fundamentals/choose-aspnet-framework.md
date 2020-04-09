---
title: Vyberte si mezi ASP.NET 4.x a ASP.NET Core
author: rick-anderson
description: Vysvětluje ASP.NET Core vs. ASP.NET 4.x a jak si vybrat mezi nimi.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: a7280b59578ee1d96edeeccf9c9df0b0e4eb4eb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665240"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Vyberte si mezi ASP.NET 4.x a ASP.NET Core

ASP.NET Core je redesign ASP.NET 4.x. Tento článek uvádí rozdíly mezi nimi.

## <a name="aspnet-core"></a>Jádro ASP.NET

ASP.NET Core je open source, multiplatformní framework pro vytváření moderních cloudových webových aplikací ve Windows, macOS nebo Linuxu.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x je vyspělý framework, který poskytuje služby potřebné k vytváření webových aplikací na podnikové úrovni založených na serveru v systému Windows.

## <a name="framework-selection"></a>Výběr rámce

Následující tabulka porovnává ASP.NET Core s ASP.NET 4.x.

| Jádro ASP.NET | ASP.NET 4.x |
|---|---|
|Sestavení pro Windows, macOS nebo Linux|Sestavení pro Windows|
|[Razor Pages](xref:razor-pages/index) je doporučený přístup k vytvoření webového uživatelského uživatelského panelu od ASP.NET Core 2.x. Viz také [MVC](xref:mvc/overview), [SignalR](xref:signalr/introduction)Web [API](xref:tutorials/first-web-api)a .|Použití [webových formulářů](/aspnet/web-forms) [SignalR](/aspnet/signalr), , [MVC](/aspnet/mvc), [webového rozhraní API](/aspnet/web-api/), [webhooků](/aspnet/webhooks/)nebo [webových stránek](/aspnet/web-pages)|
|Více verzí na počítač|Jedna verze na počítač|
|Vývoj pomocí [sady Visual Studio](https://visualstudio.microsoft.com/vs/), Visual Studio for [Mac](https://visualstudio.microsoft.com/vs/mac/)nebo Kódu sady [Visual Studio](https://code.visualstudio.com/) pomocí jazyka C# nebo F #|Vývoj pomocí [sady Visual Studio](https://visualstudio.microsoft.com/vs/) pomocí jazyka C#, VB nebo F #|
|Vyšší výkon než ASP.NET 4.x|Dobrý výkon|
|[Použití runtime jádra .NET](/dotnet/standard/choosing-core-framework-server)|Použití runtime rozhraní .NET Framework|

Informace o podpoře jádra 2.x ASP.NET ASP.NET [jádra .NET Framework](xref:index#target-framework) v rozhraní .NET Framework naleznete v tématu ASP.NET.

## <a name="aspnet-core-scenarios"></a>ASP.NET základní scénáře

* [Websites](xref:tutorials/first-mvc-app/index)
* [Rozhraní API](xref:tutorials/first-web-api)
* [Reálný čas](xref:signalr/introduction)
* [Nasazení aplikace ASP.NET Core do Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET scénáře 4.x

* [Websites](/aspnet/mvc)
* [Rozhraní API](/aspnet/web-api)
* [Reálný čas](/aspnet/signalr)
* [Vytvoření webové aplikace ASP.NET 4.x v Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Další zdroje

* [Úvod do ASP.NET](/aspnet/overview)
* [Úvod do ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
