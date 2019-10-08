---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikací Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/class-libraries
ms.openlocfilehash: 2e042b43c6db24e0ecac727be100575fe1275e17
ms.sourcegitcommit: 6d26ab647ede4f8e57465e29b03be5cb130fc872
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "71999784"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core knihovny tříd součástí Razor

Od [Simon Timms](https://github.com/stimms)

Komponenty lze sdílet v [knihovně tříd Razor (RCL)](xref:razor-pages/ui-class) napříč projekty. *Knihovna tříd prvků Razor* může obsahovat:

* Jiný projekt v řešení.
* Balíček NuGet.
* Odkazovaná knihovna .NET.

Stejně jako komponenty jsou běžné typy .NET, komponenty poskytované RCL jsou normální sestavení .NET.

## <a name="create-an-rcl"></a>Vytvoření RCL

Pokud chcete nakonfigurovat prostředí pro Blazor, postupujte podle pokynů v článku o @no__t 0.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **knihovnu tříd Razor**. Vyberte **Další**.
1. V dialogovém okně **vytvořit novou knihovnu tříd Razor** vyberte **vytvořit**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název projektu `MyComponentLib1`. Vyberte **Vytvořit**.
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **přidat** > **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( *. csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **Přidat** **odkaz** > .
   1. Vyberte projekt RCL. Vyberte **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1`. Složka, která obsahuje `MyComponentLib1`, se vytvoří automaticky při spuštění příkazu:

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
* Použijte direktivu [\@Using](xref:mvc/views/razor#using) Razor. Jednotlivé komponenty lze přidat podle názvu.

V následujících příkladech je `MyComponentLib1` knihovnou komponent obsahující komponentu `SalesReport`.

Na komponentu `SalesReport` se dá odkazovat pomocí jejího úplného názvu typu s oborem názvů:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s direktivou `@using`:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Zahrňte direktivu `@using MyComponentLib1` v souboru *_Import. Razor* na nejvyšší úrovni, aby komponenty knihovny byly dostupné pro celý projekt. Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet. Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:

```dotnetcli
dotnet pack
```

Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet push](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny tříd součástí Razor se statickými prostředky

RCL může zahrnovat statické prostředky. Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
