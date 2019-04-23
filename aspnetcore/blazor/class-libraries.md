---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/class-libraries
ms.openlocfilehash: f7c9ce20bf23bc532e664764d6e48d9163db727f
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982982"
---
# <a name="razor-components-class-libraries"></a>Knihovny tříd Razor komponenty

Podle [Simon Timms](https://github.com/stimms)

Součásti můžete sdílet v knihovnách tříd Razor ve všech projektech. Je možné zahrnout z komponenty:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazované knihovny .NET.

Stejně jako regulární typy .NET jsou komponenty, jsou součástí knihovny tříd Razor normální sestavení .NET.

Použití `razorclasslib` šablony (knihovny tříd Razor) se [dotnet nové](/dotnet/core/tools/dotnet-new) příkaz:

```console
dotnet new razorclasslib -o MyComponentLib1
```

Přidat soubory součástí Razor (*.razor*) do knihovny tříd Razor.

Chcete-li přidat knihovnu do existujícího projektu, použijte [dotnet sln](/dotnet/core/tools/dotnet-sln) příkaz:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> Knihovny tříd Razor nejsou kompatibilní s Blazor aplikace v ASP.NET Core ve verzi Preview 4.
>
> K vytvoření komponenty v knihovně, kterou můžete sdílet s Blazor na straně klienta a Razor komponenty na straně serveru aplikace použijte knihovnu tříd Blazor vytvořené `blazorlib` šablony.
>
> Knihovny tříd Razor nepodporují statické prostředky v technologii ASP.NET Core ve verzi Preview 4. Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky. Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.

## <a name="consume-a-library-component"></a>Využívat komponentu knihovny

Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:

* Úplný název typu s oborem názvů.
* Syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice. Jednotlivé komponenty se můžou přidávat podle názvu.

V následujících příkladech `MyComponentLibrary` je součást Knihovna, která obsahuje prodejní sestavu (`SalesReport`) komponenty.

Komponenta prodejní sestavu můžete odkazovat pomocí jeho úplný název typu s oborem názvů:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLibrary.SalesReport />
```

Komponentu lze také odkazovat Pokud knihovny je přenesena do rozsahu pomocí `@using` – direktiva:

```cshtml
@using MyComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

`@using` Směrnice můžou být součástí *_Import.razor* k vytvoření součásti k dispozici pro celý projekt nebo použité na jednu stránku nebo sadu stránek ve složce.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, aktualizací Service pack a příjemce pro NuGet

Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget. Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkaz:

```console
dotnet pack
```

Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkaz:

```console
dotnet nuget publish
```

Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet. Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.
