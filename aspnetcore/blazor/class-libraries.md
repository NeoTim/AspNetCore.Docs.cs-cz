---
title: Knihovny tříd Razor komponenty
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2019
uid: blazor/class-libraries
ms.openlocfilehash: 9ca1d54da584c2957be98708782437e28b619e3b
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085843"
---
# <a name="razor-components-class-libraries"></a>Knihovny tříd Razor komponenty

Podle [Simon Timms](https://github.com/stimms)

Součásti můžete sdílet v knihovnách tříd Razor ve všech projektech. Je možné zahrnout z komponenty:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazované knihovny .NET.

Stejně jako regulární typy .NET jsou komponenty, jsou součástí knihovny tříd Razor normální sestavení .NET.

## <a name="create-a-razor-class-library"></a>Vytvoření knihovny tříd Razor

Postupujte podle pokynů v <xref:blazor/get-started> článku ke konfiguraci prostředí pro Blazor.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název projektu `MyComponentLib1`. Vyberte **Vytvořit**.
1. V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.
1. Vyberte **knihovny tříd Razor** šablony. Vyberte **Vytvořit**.
1. Přidání knihovny tříd Razor k řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **přidat** > **existující projekt**.
   1. Přejděte k souboru projektu knihovny tříd Razor.
   1. Vyberte soubor projektu knihovny tříd Razor (*.csproj*).
1. Přidáte odkaz knihovny tříd Razor z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **přidat** > **odkaz**.
   1. Vyberte projekt knihovny tříd Razor. Vyberte **OK**.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

1. Použití knihovny tříd Razor (`razorclasslib`) šablony s [dotnet nové](/dotnet/core/tools/dotnet-new) z příkazové okno. V následujícím příkladu je vytvořena s názvem knihovny tříd Razor `MyComponentLib1`. Složky obsahující `MyComponentLib1` je vytvořen automaticky při spuštění příkazu.

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Přidání knihovny do existujícího projektu, použijte [se příkaz dotnet add odkaz](/dotnet/core/tools/dotnet-add-reference) z příkazové okno. V následujícím příkladu knihovny tříd Razor se přidá do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

Přidat soubory součástí Razor (*.razor*) do knihovny tříd Razor.

## <a name="razor-class-libraries-not-supported-for-client-side-apps"></a>Knihovny tříd Razor není podporována pro aplikace na straně klienta

ASP.NET Core 3.0 ve verzi Preview nejsou kompatibilní s aplikacemi na straně klienta Blazor knihovny tříd Razor.

Blazor aplikace na straně klienta, použijte knihovně komponent Blazor vytvořené `blazorlib` šablonu z příkazové prostředí:

```console
dotnet new blazorlib -o MyComponentLib1
```

Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení (*.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky. Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.

## <a name="static-assets-not-supported-for-server-side-apps"></a>Statické prostředky nejsou podporovány pro aplikace na straně serveru

Ve verzi Preview pro aplikaci ASP.NET Core 3.0 Blazor serverové aplikace moci spotřebovat statické prostředky z obou knihovny tříd Razor (`razorclasslib`) nebo knihovny Blazor (`blazorlib`).

Jako dočasné řešení, můžete zkusit [BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/).

> [!NOTE]
> [BlazorEmbedLibrary](https://www.nuget.org/packages/BlazorEmbedLibrary/) není zachována nebo podporovaný společností Microsoft.

## <a name="consume-a-library-component"></a>Využívat komponentu knihovny

Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:

* Použijte úplný název typu s oborem názvů.
* Použití syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice. Jednotlivé komponenty mohou být přidány podle názvu.

V následujících příkladech `MyComponentLib1` je součást Knihovna, která obsahuje prodejní sestavu (`SalesReport`) komponenty.

Komponenta prodejní sestavu můžete odkazovat pomocí jeho úplný název typu s oborem názvů:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Komponentu lze také odkazovat Pokud knihovny je přenesena do rozsahu pomocí `@using` – direktiva:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Zahrnout `@using MyComponentLib1` direktivu na nejvyšší úrovni *_Import.razor* souboru pro zpřístupnění knihovny součásti pro celý projekt. Přidat direktivu do *_Import.razor* souboru na libovolné úrovni použít obor názvů na jednu stránku nebo sadu stránek ve složce.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, aktualizací Service pack a příjemce pro NuGet

Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget. Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) z příkazové prostředí:

```console
dotnet pack
```

Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) z příkazové prostředí:

```console
dotnet nuget publish
```

Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet. Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky. Všimněte si, že [statických prostředků se nepodporují pro aplikace na straně serveru](#static-assets-not-supported-for-server-side-apps), včetně toho, když Blazor knihovny (`blazorlib`) odkazuje na aplikaci na straně serveru.
