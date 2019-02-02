---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí aplikace Razor komponenty z externí komponenta knihovny.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/class-libraries
ms.openlocfilehash: e43755f606d2c843fe6b12f06e1e96626e0f3cdf
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668094"
---
# <a name="razor-components-class-libraries"></a>Knihovny tříd Razor komponenty

Podle [Simon Timms](https://github.com/stimms)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

> [!NOTE]
> Sada .NET Core 3.0 ve verzi Preview 2 SDK neobsahuje šablona projektu pro knihovny tříd Razor součásti, ale chceme přidat šablonu v budoucí verzi preview. Mezitím můžete použít šablony knihovna tříd komponenty Blazor vysvětlované v tomto tématu.

Součástí je sdílet v knihovnách komponenty ve všech projektech. Je možné zahrnout z komponenty:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazované knihovny .NET.

Stejně, jako jsou komponenty regulární typy .NET, jsou součástí knihovny normální sestavení .NET.

K vytvoření nové komponenty knihovny, použijte `blazorlib` šablonu s [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu. Šablona je součástí šablony nainstalované při [nastavení komponent Razor](/docs/get-started.html#setup).

```console
dotnet new blazorlib -o MyComponentLib1
```

Chcete-li přidat knihovnu do existujícího projektu, použijte [dotnet sln](/dotnet/core/tools/dotnet-sln) příkaz:

```console
dotnet sln add .\MyComponentLib1
```

Komponenta knihovny mohou obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky. Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek. 

## <a name="consume-a-library-component"></a>Využívat komponentu knihovny

Aby bylo možné využívat součásti definované v knihovně v jiném projektu [ @addTagHelper ](/aspnet/core/mvc/views/tag-helpers/intro#add-helper-label) musí se použít direktiva. Jednotlivé komponenty se můžou přidávat podle názvu. Například následující direktivy přidává `Component1` z `MyComponentLib1`:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

Obecný formát direktivy je:

```cshtml
@addTagHelper <namespaced component name>, <assembly name>
```

Je však společné pro všechny součásti ze sestavení pomocí zástupného znaku patří:

```cshtml
@addTagHelper *, MyComponentLib1
```

`@addTagHelper` Směrnice můžou být součástí *_ViewImport.cshtml* k vytvoření součásti k dispozici pro celý projekt nebo použité na jednu stránku nebo sadu stránek ve složce. S `@addTagHelper` direktiv v místě, součástí knihovny součástí mohou být spotřebovány jako kdyby byly ve stejném sestavení jako aplikace. 

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, aktualizací Service pack a příjemce pro NuGet

Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget. Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkaz:

```console
dotnet pack
```

Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkaz:

```console
dotnet nuget publish
```

Žádné zahrnuté statické prostředky jsou obsažené v balíčku NuGet. Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.
