---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikace Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: 32088b43f91174596f6b9251d36782e806f966b9
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213246"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core knihovny tříd součástí Razor

Od [Simon Timms](https://github.com/stimms)

Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. *Knihovna tříd prvků Razor* může obsahovat:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

Pokud chcete nakonfigurovat prostředí pro Blazor, postupujte podle pokynů v článku o <xref:blazor/get-started>.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvoření nového projektu
1. Vyberte **knihovnu tříd Razor**. Vyberte **Další**.
1. V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Příklady v tomto tématu používají název projektu `MyComponentLib1`. Vyberte **Create** (Vytvořit).
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **přidat** > **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( *. csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

> [!NOTE]
> Pokud je zaškrtnuté políčko **stránky podpory a zobrazení** při generování RCL ze šablony, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožní vytváření komponent Razor:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ručně přidejte soubor do kořenového adresáře vygenerovaného projektu.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořena RCL s názvem `MyComponentLib1`. Složka, která obsahuje `MyComponentLib1`, je vytvořena automaticky při spuštění příkazu:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Pokud se při generování RCL ze šablony používá přepínač `-s|--support-pages-and-views`, přidejte také soubor *_Imports. Razor* do kořenového adresáře vygenerovaného projektu s následujícím obsahem, který umožňuje vytváření komponent Razor:
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
* Pomocí direktivy [using\@](xref:mvc/views/razor#using) Razor. Jednotlivé komponenty lze přidat podle názvu.

V následujících příkladech je `MyComponentLib1` knihovnou komponent obsahující `SalesReport` komponentu.

Na součást `SalesReport` lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:

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

Zahrňte direktivu `@using MyComponentLib1` do souboru *_Import. Razor* nejvyšší úrovně, aby komponenty knihovny byly dostupné pro celý projekt. Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet. Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:

```dotnetcli
dotnet pack
```

Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny tříd součástí Razor se statickými prostředky

RCL může zahrnovat statické prostředky. Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají. Další informace najdete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:razor-pages/ui-class>
