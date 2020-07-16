---
title: Nástroje pro ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si informace o nástrojích dostupných pro sestavování Blazor aplikací.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 33245e669b317ed577a8a1652b2eed8f9ea5b915
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2020
ms.locfileid: "86407642"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Nástroje pro ASP.NET CoreBlazor

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

::: zone pivot="os-windows"

1. Nainstalujte nejnovější verzi sady [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) s úlohou **vývoje ASP.NET a webu** .

1. Vytvoření nového projektu

1. Vyberte možnost ** Blazor aplikace**. Vyberte **Next** (Další).

1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Potvrďte správnost záznamu **umístění** nebo zadejte umístění projektu. Vyberte **Vytvořit**.

1. Pro Blazor WebAssembly prostředí vyberte šablonu ** Blazor WebAssembly aplikace** . Pro Blazor Server prostředí vyberte šablonu ** Blazor Server aplikace** . Vyberte **Vytvořit**.

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .

Další informace o důvěřování certifikátu pro vývoj ASP.NET Core HTTPS najdete v tématu <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end

::: zone pivot="os-linux"

1. Nainstalujte nejnovější verzi sady [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Pokud jste dříve nainstalovali sadu SDK, můžete zjistit nainstalovanou verzi spuštěním následujícího příkazu v příkazovém prostředí:

   ```dotnetcli
   dotnet --version
   ```

1. Nainstalujte nejnovější verzi [Visual Studio Code](https://code.visualstudio.com/).

1. Nainstalujte nejnovější [rozšíření C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Pro Blazor WebAssembly prostředí spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pro Blazor Server prostředí spusťte následující příkaz v příkazovém prostředí:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Otevřete `WebApplication1` složku v Visual Studio Code.

1. Rozhraní IDE požaduje, abyste přidali prostředky pro sestavení a ladění projektu. Vyberte **Ano**.

1. Spusťte aplikaci stisknutím klávesy <kbd>CTRL</kbd> + <kbd>F5</kbd> .

## <a name="trust-a-development-certificate"></a>Důvěryhodnost vývojového certifikátu

Neexistuje žádný centralizovaný způsob, jak důvěřovat certifikátu v systému Linux. Obvykle se přijme jedna z následujících přístupů:

* Vylučte adresu URL aplikace v seznamu vyloučení v prohlížeči.
* Důvěřovat všem certifikátům podepsaným svým držitelem pro `localhost` .
* Přidejte certifikát do seznamu důvěryhodných certifikátů v prohlížeči.

Další informace najdete v pokynech, které poskytuje váš prohlížeč a distribuce systému Linux.

::: zone-end

::: zone pivot="os-macos"

1. Nainstalujte [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Vyberte **soubor**  >  **nové řešení** nebo vytvořte **Nový** projekt z **okna Start**.

1. Na bočním panelu vyberte **Webová a konzolová**  >  **aplikace**.

   Pro Blazor WebAssembly prostředí vyberte šablonu ** Blazor WebAssembly aplikace** . Pro Blazor Server prostředí vyberte šablonu ** Blazor Server aplikace** . Vyberte **Next** (Další).

   Informace o těchto dvou Blazor modelech hostování *Blazor WebAssembly* a naleznete *Blazor Server* v tématu <xref:blazor/hosting-models> .

1. Potvrďte, že **ověřování** je nastaveno na **bez ověřování**. Vyberte **Next** (Další).

1. Do pole **název projektu** název aplikace `WebApplication1` . Vyberte **Vytvořit**.

1. **Run**  >  Chcete-li spustit aplikaci *bez ladicího programu*, vyberte možnost spustit**bez ladění** . Spusťte aplikaci pomocí **příkazového**  >  **počátečního ladění** nebo tlačítka Spustit (&#9654;), aby se aplikace spustila *s ladicím programem*.

Pokud se zobrazí výzva k důvěřování vývojovým certifikátům, důvěřujete certifikátu a pokračujte. Certifikát uživatele a řetězce klíčů je nutný k důvěřování certifikátu. Další informace o důvěřování certifikátu pro vývoj ASP.NET Core HTTPS najdete v tématu <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> .

::: zone-end
