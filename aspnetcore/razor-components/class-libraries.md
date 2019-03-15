---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí aplikace Razor komponenty z externí komponenta knihovny.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978311"
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

Přidat soubory Razor komponent (*.razor*) do knihovny tříd Razor.

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
> Knihovny tříd Razor nejsou kompatibilní s Blazor aplikace v ASP.NET Core ve verzi Preview 3.
>
> Vytváření komponent v knihovně, kterou můžete sdílet s Blazor a Razor součásti aplikace, použijte knihovnu tříd Blazor vytvořené `blazorlib` šablony.
>
> Knihovny tříd Razor nepodporují statické prostředky v ASP.NET Core ve verzi Preview 3. Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky. Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.

## <a name="consume-a-library-component"></a>Využívat komponentu knihovny

Aby bylo možné využívat součásti definované v knihovně v jiném projektu [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) musí se použít direktiva. Jednotlivé komponenty se můžou přidávat podle názvu.

Obecný formát direktivy je:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Například následující direktivy přidává `Component1` z `MyComponentLib1`:

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

Je však společné pro všechny součásti ze sestavení pomocí zástupného znaku zahrnují (`*`):

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

Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet. Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.
