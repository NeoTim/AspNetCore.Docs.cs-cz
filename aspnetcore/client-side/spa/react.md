---
title: Použijte šablonu projektu reakce s ASP.NET Core
author: SteveSandersonMS
description: Naučte se, jak začít pracovat s ASP.NET Core šablonou projektu s jednou stránkou (SPA) pro reakci a vytváření reakce na aplikaci.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 0e61c5b3e31a0b050d356b8f8e16306dc1e2a7f3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080418"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Použijte šablonu projektu reakce s ASP.NET Core

Aktualizovaná šablona projektu reakce poskytuje pohodlný výchozí bod pro ASP.NET Core aplikace pomocí konvencí reakce a [vytváření reakce aplikace](https://github.com/facebookincubator/create-react-app) (CRA) pro implementaci bohatě náročného uživatelského rozhraní (UI) na straně klienta.

Šablona je ekvivalentní k vytváření ASP.NET Core projektu, který se má chovat jako back-end rozhraní API, a standardní projekt CRA reaguje na fungování jako uživatelské rozhraní, ale s pohodlíou hostování v jediném projektu aplikace, který je možné sestavit a publikovat jako jeden celek.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

Pokud máte nainstalovanou ASP.NET Core 2,1, není nutné instalovat šablonu projektu reakce.

Vytvoří nový projekt z příkazového řádku pomocí příkazu `dotnet new react` v prázdném adresáři. Například následující příkazy vytvoří aplikaci v adresáři *My-New-App* a přepne do tohoto adresáře:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Spusťte aplikaci buď ze sady Visual Studio, nebo z .NET Core CLI:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Otevřete vygenerovaný soubor *. csproj* a z něj spusťte aplikaci jako normální.

Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Ujistěte se, že máte proměnnou prostředí `ASPNETCORE_Environment` s názvem s `Development`hodnotou. V systému Windows (v případě výzev mimo PowerShell) spusťte `SET ASPNETCORE_Environment=Development`příkaz. V systému Linux nebo macOS spusťte `export ASPNETCORE_Environment=Development`.

Spusťte [sestavení dotnet](/dotnet/core/tools/dotnet-build) pro správné ověření sestavení vaší aplikace. Při prvním spuštění proces sestavení obnoví závislosti NPM, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

Spuštěním příkazu [dotnet](/dotnet/core/tools/dotnet-run) spusťte aplikaci.

---

Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci, která reaguje. Aplikace ASP.NET Core je určena k použití pro přístup k datům, autorizaci a další aspekty na straně serveru. Aplikace reaguje, která je umístěná v podadresáři *clientapp* , je určena k použití pro všechny uživatelské rozhraní.

## <a name="add-pages-images-styles-modules-etc"></a>Přidat stránky, obrázky, styly, moduly atd.

Adresář *clientapp* je standardní aplikace reagující na CRA. Další informace najdete v oficiální [dokumentaci k CRA](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) .

Existují mírné rozdíly mezi aplikací s reakci, kterou vytvořila Tato šablona, a ta, kterou vytvořila sama CRA; Možnosti aplikace se ale nezměnily. Aplikace vytvořená šablonou obsahuje rozložení založené na [bootstrap](https://getbootstrap.com/)a základní příklad směrování.

## <a name="install-npm-packages"></a>Instalace balíčků npm

K instalaci balíčků npm třetích stran použijte příkazový řádek v podadresáři *clientapp* . Příklad:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikování a nasazení

Ve vývoji se aplikace spouští v režimu optimalizovaném pro usnadnění vývoje. Například sady prostředků JavaScriptu obsahují zdrojové mapy (takže při ladění uvidíte původní zdrojový kód). Aplikace sleduje změny souborů JavaScriptu, HTML a CSS na disku a automaticky překompiluje a znovu načte, když se tyto soubory uvidí.

V produkčním prostředí poskytuje verzi vaší aplikace optimalizované pro výkon. To se nakonfiguruje tak, aby se automaticky stalo. Při publikování generuje konfigurace sestavení minifikovaného, převedený Build kódu na straně klienta. Na rozdíl od sestavení pro vývoj není v produkčním sestavení nutné nainstalovat Node. js na server.

Můžete použít standardní [ASP.NET Core hostování a metody nasazení](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Spustit CRA Server nezávisle

Projekt je nakonfigurován tak, aby spouštěl svou vlastní instanci CRA vývojového serveru na pozadí, když se aplikace ASP.NET Core spustí v režimu vývoje. To je užitečné, protože to znamená, že nemusíte spouštět samostatný server ručně.

Tato výchozí instalace je nevýhodná. Pokaždé, když upravíte C# kód a ASP.NET Core aplikace se musí restartovat, server CRA se restartuje. Spuštění zálohování vyžaduje několik sekund. Pokud provádíte časté C# úpravy kódu a nechcete čekat na RESTARTOVÁNÍ serveru CRA, spusťte server CRA externě, nezávisle na procesu ASP.NET Core. Postup:

1. Do podadresáře *clientapp* přidejte soubor *. env* s následujícím nastavením:

    ```
    BROWSER=none
    ```

    Tím zabráníte otevření webového prohlížeče při externém spuštění serveru CRA.

2. V příkazovém řádku přejděte do podadresáře *clientapp* a spusťte vývojový server CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Upravte aplikaci ASP.NET Core tak, aby používala externí instanci serveru CRA místo spuštění vlastní. Ve vaší *spouštěcí* třídě nahraďte `spa.UseReactDevelopmentServer` vyvolání následujícím způsobem:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Když spustíte aplikaci ASP.NET Core, nespustí se server CRA. Místo toho se použije instance, kterou jste spustili ručně. To umožňuje, aby se rychleji spouštěla a restartovala. Už nečeká na opětovné sestavení vaší aplikace v reakci.

> [!IMPORTANT]
> "Vykreslování na straně serveru" není podporovanou funkcí této šablony. Naším cílem této šablony je vyhovět paritě pomocí příkazu "vytvořit reakci-aplikace". Například scénáře a funkce, které nejsou součástí projektu "vytvoření reakce na aplikaci" (například SSR), nejsou podporovány a jsou pro uživatele ponechány jako cvičení.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/identity/spa>
