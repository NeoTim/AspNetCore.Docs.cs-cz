---
title: ASP.NET Core Razor komponenty knihovny třídy
author: guardrex
description: Zjistěte, jak komponenty mohou být součástí Blazor aplikací z knihovny externí komponenta.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: e99dd63200dc863552f099b5d715f78a9732165c
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538505"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor komponenty knihovny třídy

Podle [Simon Timms](https://github.com/stimms)

Součástí je možné sdílet v [knihovny tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. A *knihovna tříd Razor komponenty* půjdou zahrnout od:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazované knihovny .NET.

Stejně, jako jsou komponenty regulární typy .NET, jsou součástí RCL normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

Postupujte podle pokynů v <xref:blazor/get-started> článku ke konfiguraci prostředí pro Blazor.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Zadejte název projektu **název projektu** pole nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název projektu `MyComponentLib1`. Vyberte **Vytvořit**.
1. V **vytvořit novou webovou aplikaci ASP.NET Core** dialogového okna, ujistěte se, že **.NET Core** a **ASP.NET Core 3.0** jsou vybrány.
1. Vyberte **knihovny tříd Razor** šablony. Vyberte **Vytvořit**.
1. Přidáte do řešení RCL:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **přidat** > **existující projekt**.
   1. Přejděte k souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( *.csproj*).
1. Přidejte odkaz RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core CLI](#tab/visual-studio-code+netcore-cli)

1. Použití **knihovny tříd Razor** šablony (`razorclasslib`) se [dotnet nové](/dotnet/core/tools/dotnet-new) příkazu v příkazovém řádku. V následujícím příkladu je vytvořena s názvem RCL `MyComponentLib1`. Složky obsahující `MyComponentLib1` je vytvořen automaticky při spuštění příkazu:

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Chcete-li přidat knihovnu do existujícího projektu, použijte [se příkaz dotnet add odkaz](/dotnet/core/tools/dotnet-add-reference) příkazu v příkazovém řádku. V následujícím příkladu se přidá RCL do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a>RCLs není podporována pro aplikace na straně klienta

V aktuální ASP.NET Core náhledu 3.0 nejsou kompatibilní s aplikacemi na straně klienta Blazor knihovny tříd Razor. Blazor aplikace na straně klienta, použijte knihovně komponent Blazor vytvořené `blazorlib` šablony v příkazovém řádku:

```console
dotnet new blazorlib -o MyComponentLib1
```

Pomocí knihovny součástí `blazorlib` šablona může obsahovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V okamžiku sestavení, statické soubory jsou vloženy do souborů sestavení ( *.dll*), která umožňuje využití komponent bez nutnosti starat o tom, jak zahrnout svoje prostředky. Všechny soubory zahrnuté v `content` adresáře jsou označeny jako vložený prostředek.

## <a name="consume-a-library-component"></a>Využívat komponentu knihovny

Aby bylo možné využívat součásti definované v knihovně v jiném projektu, použijte některou z následujících postupů:

* Použijte úplný název typu s oborem názvů.
* Použití syntaxe Razor pro [ \@pomocí](xref:mvc/views/razor#using) směrnice. Jednotlivé komponenty mohou být přidány podle názvu.

V následujících příkladech `MyComponentLib1` je součástí knihovny obsahující `SalesReport` komponenty.

`SalesReport` Komponenty lze odkazovat pomocí jeho úplný název typu s oborem názvů:

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

Protože jsou součástí knihovny knihovny .NET standard, balení a je na cestě k NuGet se nijak neliší od balení a přesouvání všechny knihovny nuget. Balení se provádí pomocí [balíčku dotnet](/dotnet/core/tools/dotnet-pack) příkazu v příkazovém řádku:

```console
dotnet pack
```

Nahrání balíčku NuGet pomocí [dotnet nuget publikovat](/dotnet/core/tools/dotnet-nuget-push) příkazu v příkazovém řádku:

```console
dotnet nuget publish
```

Při použití `blazorlib` šablonu, statických prostředků jsou obsažené v balíčku NuGet. Příjemci knihovny automaticky obdrží skripty a šablony stylů, takže příjemci nejsou potřeba ručně instalovat prostředky.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny tříd Razor součásti s statické prostředky

RCL můžete zahrnout statické prostředky. Statické prostředky jsou dostupné pro každou aplikaci, která využívá knihovny. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
