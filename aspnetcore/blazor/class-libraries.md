---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikace Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f8e8688cdb3d1aef0d470e0e2d8c3857140ef65f
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160025"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core knihovny tříd součástí Razor

Od [Simon Timms](https://github.com/stimms)

Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. *Knihovna tříd prvků Razor* může obsahovat:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

Podle pokynů v článku o <xref:blazor/get-started> můžete nakonfigurovat prostředí pro Blazor.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **knihovnu tříd Razor**. Vyberte **Další**.
1. V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Příklady v tomto tématu používají název projektu `MyComponentLib1`. Vyberte **vytvořit**.
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **přidat** > **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( *. csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořena RCL s názvem `MyComponentLib1`. Složka, která obsahuje `MyComponentLib1`, je vytvořena automaticky při spuštění příkazu:

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

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
