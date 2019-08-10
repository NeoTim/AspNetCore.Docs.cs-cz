---
title: ASP.NET Core knihovny tříd součástí Razor
author: guardrex
description: Zjistěte, jak mohou být součásti součástí aplikací Blazor z externí knihovny součástí.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/class-libraries
ms.openlocfilehash: 402b7b072554f63f85e7cf5e55336104d235a071
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913906"
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

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. V příkladech v tomto tématu se používá název `MyComponentLib1`projektu. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .
1. Vyberte šablonu **knihovny tříd Razor** . Vyberte **Vytvořit**.
1. Přidat RCL do řešení:
   1. Klikněte pravým tlačítkem na řešení. Vyberte **Přidat** > **existující projekt**.
   1. Přejděte do souboru projektu RCL.
   1. Vyberte soubor projektu RCL ( *. csproj*).
1. Přidejte odkaz na RCL z aplikace:
   1. Klikněte pravým tlačítkem na projekt aplikace. Vyberte **Přidat** > **odkaz**.
   1. Vyberte projekt RCL. Vyberte **OK**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

1. Použijte šablonu **knihovny tříd Razor** (`razorclasslib`) pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí. V následujícím příkladu je vytvořen RCL s názvem `MyComponentLib1`. Složka, která je `MyComponentLib1` uložena, je vytvořena automaticky při spuštění příkazu:

   ```console
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. Chcete-li přidat knihovnu do existujícího projektu, použijte příkaz [dotnet Add Reference](/dotnet/core/tools/dotnet-add-reference) v příkazovém prostředí. V následujícím příkladu se RCL přidá do aplikace. Spusťte následující příkaz ze složky projektu aplikace s cestou ke knihovně:

   ```console
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="rcls-not-supported-for-client-side-apps"></a>RCLs se nepodporuje pro aplikace na straně klienta.

V aktuálním ASP.NET Core 3,0 Preview nejsou knihovny tříd Razor kompatibilní s Blazor aplikacemi na straně klienta. Pro Blazor aplikace na straně klienta použijte Blazor knihovnu komponent vytvořenou `blazorlib` šablonou v příkazovém prostředí:

```console
dotnet new blazorlib -o MyComponentLib1
```

Knihovny komponent, které `blazorlib` používají šablonu, můžou zahrnovat statické soubory, jako jsou obrázky, JavaScript a šablony stylů. V době sestavování jsou statické soubory vloženy do sestaveného souboru sestavení ( *. dll*), což umožňuje spotřebovat komponenty bez obav, jak zahrnout jejich prostředky. Všechny soubory zahrnuté v `content` adresáři jsou označené jako vložené prostředky.

## <a name="consume-a-library-component"></a>Využití komponenty knihovny

Aby bylo možné využívat komponenty definované v knihovně v jiném projektu, použijte některý z následujících přístupů:

* Použijte úplný název typu s oborem názvů.
* Použijte direktivu [ \@using](xref:mvc/views/razor#using) Razor. Jednotlivé komponenty lze přidat podle názvu.

V následujících příkladech `MyComponentLib1` je knihovna komponent `SalesReport` obsahující komponentu.

Na `SalesReport` komponentu lze odkazovat pomocí jejího úplného názvu typu s oborem názvů:

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Na komponentu lze také odkazovat, je-li knihovna přenesena do rozsahu s `@using` direktivou:

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Zahrňte direktivu do souboru *_Import. Razor* na nejvyšší úrovni, aby komponenty knihovny byly dostupné pro celý projekt. `@using MyComponentLib1` Přidáním direktivy do souboru *_Import. Razor* na libovolné úrovni můžete použít obor názvů na jednu stránku nebo sadu stránek v rámci složky.

## <a name="build-pack-and-ship-to-nuget"></a>Sestavení, balení a odeslání do NuGet

Knihovny součástí jsou standardní knihovny .NET, balení a jejich odeslání do NuGet se neliší od balení a předává jakékoli knihovny do NuGet. Balení se provádí pomocí příkazu [dotnet Pack](/dotnet/core/tools/dotnet-pack) v příkazovém prostředí:

```console
dotnet pack
```

Nahrajte balíček do NuGet pomocí příkazu [dotnet NuGet pro publikování](/dotnet/core/tools/dotnet-nuget-push) v příkazovém prostředí:

```console
dotnet nuget publish
```

Při použití `blazorlib` šablony jsou do balíčku NuGet zahrnuty statické prostředky. Příjemci knihovny automaticky přijímají skripty a šablony stylů, takže uživatelé nejsou k ruční instalaci prostředků potřebovat.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Vytvoření knihovny tříd součástí Razor se statickými prostředky

RCL může zahrnovat statické prostředky. Statické prostředky jsou k dispozici pro všechny aplikace, které knihovnu využívají. Další informace naleznete v tématu <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="additional-resources"></a>Další zdroje

* <xref:razor-pages/ui-class>
