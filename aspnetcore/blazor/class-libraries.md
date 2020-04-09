---
title: ASP.NET knihovny tříd komponent Core Razor
author: guardrex
description: Zjistěte, jak lze Blazor komponenty zahrnout do aplikací z knihovny externích komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218763"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET knihovny tříd komponent Core Razor

Podle [Simon Timms](https://github.com/stimms)

Součásti mohou být sdíleny v [knihovně třídy Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. Knihovnu *tříd komponent Razor* lze zahrnout z:

* Další projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako součásti jsou běžné typy .NET, součásti poskytované rcl jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření rcl

Postupujte podle <xref:blazor/get-started> pokynů v článku nakonfigurovat prostředí pro Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu
1. Vyberte **knihovnu tříd razor**. Vyberte **další**.
1. V **dialogovém** okně Vytvořit novou knihovnu tříd Razor vyberte **Vytvořit**.
1. Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu. Příklady v tomto tématu `MyComponentLib1`používají název projektu . Vyberte **Vytvořit**.
1. Přidejte RCL do řešení:
   1. Klikněte pravým tlačítkem myši na řešení. Vyberte **Přidat** > **existující projekt**.
   1. Přejděte do souboru projektu rcl.
   1. Vyberte soubor projektu RCL (*.csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem myši na projekt aplikace. Vyberte **Přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

> [!NOTE]
> Pokud je při generování rcl ze šablony zaškrtnuto **políčko Stránky podpory a zobrazení,** přidejte také soubor *_Imports.razor* do kořenového adresáře generovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ručně přidejte kořenový adresář generovaného projektu.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **Knihovny tříd razor** (`razorclasslib`) s novým příkazem [dotnet](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořen `MyComponentLib1`rcl s názvem . Složka, která `MyComponentLib1` obsahuje, je vytvořena automaticky při spuštění příkazu:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Pokud `-s|--support-pages-and-views` se přepínač používá při generování RCL ze šablony, přidejte také soubor *_Imports.razor* do kořenového adresáře generovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ručně přidejte kořenový adresář generovaného projektu.

1. Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí. V následujícím příkladu rcl je přidán do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Využití komponenty knihovny

Chcete-li využívat součásti definované v knihovně v jiném projektu, použijte některý z následujících přístupů:

* Použijte úplný název typu s oborem názvů.
* Použijte Razor [ \@pomocí](xref:mvc/views/razor#using) směrnice. Jednotlivé součásti lze přidat podle názvu.

V následujících příkladech `MyComponentLib1` je knihovna `SalesReport` komponent obsahující komponentu.

Na `SalesReport` komponentu lze odkazovat pomocí úplného názvu typu s oborem názvů:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Komponenta může být také odkazováno, pokud `@using` je knihovna uvedena do oboru pomocí směrnice:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Zahrnout `@using MyComponentLib1` direktivu do souboru *_Import.razor* nejvyšší úrovně, aby byly komponenty knihovny k dispozici pro celý projekt. Přidejte direktivu do souboru *_Import.razor* na libovolné úrovni, abyste použili obor názvů na jednu stránku nebo sadu stránek ve složce.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny tříd komponent Razor se statickými datovými zdroji

RCL může obsahovat statické prostředky. Statické datové zdroje jsou k dispozici pro všechny aplikace, které spotřebovává knihovnu. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Vzhledem k tomu, že knihovny komponent jsou standardní knihovny .NET, balení a jejich odesílání do NuGet se nijak neliší od balení a odesílání libovolné knihovny do NuGet. Balení se provádí pomocí příkazu [dotnet pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:

```dotnetcli
dotnet pack
```

Nahrajte balíček do NuGet pomocí příkazu [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
* [Přidání konfiguračního souboru linkeru XML do knihovny](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
