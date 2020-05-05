---
title: Knihovny Razor tříd ASP.NET Core komponenty
author: guardrex
description: Zjistěte, jak mohou být součásti součástí Blazor aplikací z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 57f3494fd825b6549c40f56962da2c8076e8fd51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767093"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core knihovny tříd součástí Razor

Od [Simon Timms](https://github.com/stimms)

Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. *Knihovna tříd prvků Razor* může obsahovat:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

Pokud chcete nakonfigurovat prostředí pro <xref:blazor/get-started> Blazor, postupujte podle pokynů v článku.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu
1. Vyberte **knihovnu tříd Razor**. Vyberte **Další**.
1. V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název `MyComponentLib1`projektu. Vyberte **Vytvořit**.
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **Přidat** > **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL (*. csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **Přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

> [!NOTE]
> Pokud je zaškrtnuté políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1`. Složka, která je `MyComponentLib1` uložena, je vytvořena automaticky při spuštění příkazu:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Pokud je `-s|--support-pages-and-views` při generování RCL ze šablony použit přepínač, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožňuje vytváření komponent Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.

1. Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí. V následujícím příkladu se RCL přidá do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Využití komponenty knihovny

Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:

* Použijte úplný název typu s oborem názvů.
* Použijte Razordirektivu [ \@using](xref:mvc/views/razor#using) . Jednotlivé komponenty lze přidat podle názvu.

V následujících příkladech `MyComponentLib1` je knihovna komponent obsahující `SalesReport` komponentu.

Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Zahrňte `@using MyComponentLib1` direktivu do souboru *_Import. Razor* na nejvyšší úrovni, aby komponenty knihovny byly dostupné pro celý projekt. Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny Razor tříd komponent se statickými prostředky

RCL může zahrnovat statické prostředky. Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet. Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:

```dotnetcli
dotnet pack
```

Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
* [Přidání konfiguračního souboru linkeru XML do knihovny](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
