---
title: Šablona projektu Angular s ASP.NET Core
author: SteveSandersonMS
description: Zjistěte, jak začít pracovat se šablonou projektu ASP.NET Core jedné stránky aplikace (SPA) zaujetí pro Angular a Angular CLI.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/13/2019
uid: spa/angular
ms.openlocfilehash: 35a839e31369e8dbf00f5dbfb3751a2985335755
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248118"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Šablona projektu Angular s ASP.NET Core

Šablona projektu aktualizované Angular poskytuje příhodný výchozí bod pro ASP.NET Core pomocí Angular a Angular CLI k implementaci bohatě vybaveným a na straně klienta uživatelské rozhraní (UI) aplikace.

Šablona je ekvivalentní k vytváření projektu aplikace ASP.NET Core tak, aby fungoval jako back-endu rozhraní API a projekt Angular CLI tak, aby fungoval jako uživatelské rozhraní. Šablona nabízí praktické hostování oba typy projektů v projektu aplikace s jedním. V důsledku toho projekt aplikace dají vytvořit a publikovat jako jeden celek.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

Pokud máte ASP.NET Core 2.1 nainstalovaný, není nutné k instalaci šablona projektu Angular.

Vytvoření nového projektu z příkazového řádku pomocí příkazu `dotnet new angular` v prázdném adresáři. Například následující příkazy vytvoří aplikaci *my nové app* adresáře a přepnete se do tohoto adresáře:

```console
dotnet new angular -o my-new-app
cd my-new-app
```

Spuštění aplikace Visual Studio nebo .NET Core CLI:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

Otevřete vygenerovaný *.csproj* souboru a spuštění aplikace jako za normálních okolností z něj.

Proces sestavení obnoví závislosti npm při prvním spuštění, což může trvat několik minut. Následující sestavení jsou mnohem rychlejší.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Ujistěte se, máte proměnnou prostředí volá `ASPNETCORE_Environment` s hodnotou `Development`. Na Windows (v výzev – prostředí PowerShell), spusťte `SET ASPNETCORE_Environment=Development`. V systému macOS nebo Linux spusťte `export ASPNETCORE_Environment=Development`.

Spustit [dotnet sestavení](/dotnet/core/tools/dotnet-build) správně k ověření aplikace sestavena. Při prvním spuštění procesu sestavení obnoví závislosti na npm, což může trvat několik minut. Následující sestavení jsou mnohem rychlejší.

Spustit [dotnet spustit](/dotnet/core/tools/dotnet-run) a spusťte aplikaci. Bude zaznamenána zpráva podobná této:

```console
Now listening on: http://localhost:<port>
```

Přejděte na tuto adresu URL v prohlížeči.

Spuštění aplikace instance serveru Angular CLI na pozadí. Bude zaznamenána zpráva podobná této: *NG Live vývojový Server naslouchá na localhost:&lt;otherport&gt;, otevřete prohlížeč na http://localhost:&lt; otherport&gt;/*. Tuto zprávu ignorovat&mdash;má **není** adresu URL pro kombinované aplikace ASP.NET Core a Angular CLI.

---

Šablona projektu vytvoří aplikace ASP.NET Core a aplikaci Angular. Aplikace ASP.NET Core je určena pro použití pro přístup k datům, autorizaci a další aspekty na straně serveru. Aplikaci Angular, které se nacházejí v *ClientApp* podadresář, je určena pro použití pro všechny aspekty uživatelského rozhraní.

## <a name="add-pages-images-styles-modules-etc"></a>Přidání stránek, obrázků, styly, moduly, atd.

*ClientApp* adresář obsahuje standardní aplikaci Angular CLI. Najdete v oficiální [Angular dokumentaci](https://github.com/angular/angular-cli/wiki) Další informace.

Existují mírné rozdíly mezi aplikaci Angular, které jsou vytvořené pomocí této šablony a vytvořeny pomocí Angular CLI (prostřednictvím `ng new`); nicméně jsou funkcí aplikace beze změny. Obsahuje aplikaci vytvořenou pomocí šablony [Bootstrap](https://getbootstrap.com/)– na základě rozložení a základní příklad směrování.

## <a name="run-ng-commands"></a>Spusťte příkazy ng

V příkazovém řádku přejděte *ClientApp* podadresáři:

```console
cd ClientApp
```

Pokud máte `ng` globálně nainstalovaný nástroj, můžete spustit některý z jeho příkazů. Například můžete spustit `ng lint`, `ng test`, ani žádný z nich [Angular CLI příkazy](https://github.com/angular/angular-cli/wiki#additional-commands). Není nutné ke spuštění `ng serve` , protože vaše aplikace ASP.NET Core se zabývá obsluhující serverové a klientské součásti vaší aplikace. Interně používá `ng serve` ve vývoji.

Pokud nemáte k dispozici `ng` nástroj nainstalovali, spusťte `npm run ng` místo. Například můžete spustit `npm run ng lint` nebo `npm run ng test`.

## <a name="install-npm-packages"></a>Instalace balíčků npm

Pokud chcete nainstalovat balíčky npm třetích stran, použijte příkazový řádek v *ClientApp* podadresáře. Příklad:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikování a nasazení

Při vývoji se aplikace běží v režimu optimalizované pro usnadnění práce vývojářů. Například jazyka JavaScript sady obsahují zdrojových mapování (tak, aby při ladění, zobrazí se váš původním kód TypeScript). Aplikace sleduje TypeScript, HTML a CSS změny souborů na disku a automaticky se znovu zkompiluje a znovu načte, když vidí tyto soubory změnit.

V produkčním prostředí sloužit verzi vaší aplikace, které je optimalizované pro výkon. Ta se nakonfiguruje, která se provede automaticky. Když publikujete, konfigurace sestavení generuje minifikovaný, ahead-of-time (AoT) zkompilován sestavení kódu na straně klienta. Na rozdíl od sestavení vývoj nevyžaduje produkční build Node.js nainstalovaný na serveru (Pokud jste povolili [dokončení fáze před vykreslením na straně serveru](#server-side-rendering)).

Můžete použít standardní [metody hostování a nasazení ASP.NET Core](xref:host-and-deploy/index).

## <a name="run-ng-serve-independently"></a>Spuštění "ng slouží" nezávisle na sobě

Projekt je nakonfigurován ke spuštění svoji vlastní instanci serveru Angular CLI na pozadí při spuštění v režimu pro vývoj aplikace ASP.NET Core. To je vhodné, protože není nutné ručně spustit na samostatný server.

Nevýhodou této výchozí nastavení není k dispozici. Pokaždé, když změníte kód jazyka C# a vaše ASP.NET Core, které aplikace potřebuje k restartování, Angular CLI server se restartuje. Přibližně 10 sekund je potřebná ke spuštění zálohování. Pokud vytváříte časté úpravy kódu jazyka C# a nechcete čekat na restartování, Angular CLI spustit server Angular CLI externě, nezávisle na procesu ASP.NET Core. Postup:

1. V příkazovém řádku přejděte *ClientApp* podadresáře a spusťte vývojový server sady Angular CLI:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Použití `npm start` nelze spustit vývojový server Angular CLI, `ng serve`tak, aby konfigurace v *package.json* je dodržena. Chcete-li předat další parametry serveru Angular CLI, přidejte ho do příslušné `scripts` řádku v vaše *package.json* souboru.

2. Upravte aplikace ASP.NET Core pro použití namísto spuštění jednu vlastní instance externího Angular CLI. Ve vaší *spuštění* třídy, nahraďte `spa.UseAngularCliServer` vyvolání následujícím kódem:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Při spuštění aplikace ASP.NET Core se nespustí serveru Angular CLI. Místo toho se používá instanci, kterou jste spustili ručně. To umožňuje spuštění a restartování rychleji. Se už čeká Angular CLI sestavení pokaždé, když klientské aplikace.

## <a name="server-side-rendering"></a>Vykreslování na straně serveru

Jako funkce výkonu můžete předem vygenerované aplikaci Angular na serveru, jakož i v klientovi spuštěna. To znamená, že prohlížeč obdrží značka jazyka HTML představující počáteční Uživatelském rozhraní aplikace, takže se zobrazí i před stažením a spouští vaše sady JavaScript. Implementaci této pochází z Angular funkci s názvem [Angular univerzální](https://universal.angular.io/).

> [!TIP]
> Povoluje vykreslování na straně serveru (SSR) představuje celou řadou dalších komplikace i při vývoji a nasazení. Čtení [nevýhody SSR](#drawbacks-of-ssr) k určení, zda SSR je vhodné pro vaše požadavky.

Povolit SSR, budete muset vytvořit počet přidání do projektu.

V *spuštění* třídy *po* řádku, který konfiguruje `spa.Options.SourcePath`, a *před* volání `UseAngularCliServer` nebo `UseProxyToSpaDevelopmentServer`, přidejte následující:

[!code-csharp[](sample/AngularServerSideRendering/Startup.cs?name=snippet_Call_UseSpa&highlight=5-12)]

V režimu pro vývoj, tento kód se pokouší sestavení sady SSR spuštěním skriptu `build:ssr`, který je definován v *ClientApp\package.json*. Tento postup sestaví aplikaci Angular s názvem `ssr`, která ještě není definovaná.

Na konci `apps` obsahuje pole *ClientApp/.angular-cli.json*, definovat další aplikace s názvem `ssr`. Pomocí následujících možností:

[!code-json[](sample/AngularServerSideRendering/ClientApp/.angular-cli.json?range=24-41)]

Tato nová konfigurace aplikace s podporou SSR vyžaduje dva další soubory: *tsconfig.server.json* a *main.server.ts*. *Tsconfig.server.json* soubor Určuje možnosti kompilace TypeScript. *Main.server.ts* souboru slouží jako vstupní bod kódu během SSR.

Přidat nový soubor s názvem *tsconfig.server.json* uvnitř *ClientApp/src* (spolu s existující *tsconfig.app.json*), která obsahuje následující:

[!code-json[](sample/AngularServerSideRendering/ClientApp/src/tsconfig.server.json)]

Tento soubor nastaví pro Angular kompilátor AoT vyhledejte modul s názvem `app.server.module`. Přidat tak, že vytvoříte nový soubor na *ClientApp/src/app/app.server.module.ts* (spolu s existující *app.module.ts*) obsahující následující:

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/app/app.server.module.ts)]

Tento modul se dědí z vašeho klienta `app.module` a definuje, které velmi Angular moduly jsou k dispozici během SSR.

Vzpomeňte si, že nový `ssr` záznam v *.angular cli.json* odkazovaný soubor vstupního bodu s názvem *main.server.ts*. Ještě nepřidali tento soubor a nyní je čas Uděláte to tak. Vytvořte nový soubor na *ClientApp/src/main.server.ts* (spolu s existující *main.ts*), která obsahuje následující:

[!code-typescript[](sample/AngularServerSideRendering/ClientApp/src/main.server.ts)]

Tento soubor kódu je co ASP.NET Core provádí pro každý požadavek při spuštění `UseSpaPrerendering` middleware, který jste přidali do *spuštění* třídy. Se zabývá přijímají `params` z kódu .NET (jako je například adresa URL žádá) a volání rozhraní API pro Angular SSR zobrazíte výsledného souboru HTML.

Přísně anglicky mluvícího, to stačí pro povolení SSR ve vývojovém režimu. Je nezbytné, aby jeden poslední změny tak, aby vaše aplikace funguje správně při publikování. V hlavní aplikaci prvku *.csproj* souboru `BuildServerSideRenderer` hodnoty vlastnosti `true`:

[!code-xml[](sample/AngularServerSideRendering/AngularServerSideRendering.csproj?name=snippet_EnableBuildServerSideRenderer)]

Tím se nakonfiguruje proces sestavení prováděn `build:ssr` během publikování a nasazení souborů SSR k serveru. Pokud nechcete povolit, SSR selže v produkčním prostředí.

Pokud vaše aplikace běží v režimu vývojové nebo produkční prostředí, kód Angular předem vykreslí jako kódu HTML na serveru. Spustí kód na straně klienta jako obvykle.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Předání dat z kódu .NET do kódu TypeScript

Během SSR může být vhodné k předávání dat na žádost z vaší aplikace ASP.NET Core do aplikace pro Angular. Například může předat informace o souboru cookie nebo něco čtení z databáze. Chcete-li to provést, upravte vaše *spuštění* třídy. Při zpětném volání pro `UseSpaPrerendering`, nastavte hodnotu pro `options.SupplyData` jako je následující:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

`SupplyData` Zpětného volání vám umožní předat libovolný jednotlivých žádostí, serializovat JSON data (například řetězce, logické hodnoty nebo číslice). Vaše *main.server.ts* kód přijímá jako `params.data`. Například předchozí příklad kódu předá hodnotu typu boolean jako `params.data.isHttpsRequest` do `createServerRenderer` zpětného volání. To můžete předat do jiné části aplikace žádným způsobem podporuje Angular. Třeba zjistit, jak *main.server.ts* předává `BASE_URL` hodnotu pro všechny součásti, jejíž konstruktor je deklarované ho přijímat pomocí.

### <a name="drawbacks-of-ssr"></a>Nevýhody SSR

Ne všechny aplikace s výhodou SSR. Primární výhoda je vnímaný výkon. Návštěvníci dosáhnout aplikace přes pomalé připojení k síti nebo na mobilních zařízeních pomalé počáteční uživatelského rozhraní zobrazovat rychle, i v případě, že trvá při načtení nebo parsovat sady JavaScript. Ale mnoho SPA slouží především přes rychlé, interní firemní sítě na počítačích rychlé kde se zobrazí aplikace, téměř okamžitě.

Ve stejnou dobu jsou významné nevýhod povolení SSR. To zvyšuje složitost vašeho vývojového procesu. Váš kód musí běžet ve dvou různých prostředích: na straně klienta i stranu serveru (v prostředí Node.js vyvolat pomocí ASP.NET Core). Tady jsou některé věci k berte v úvahu:

* SSR vyžaduje instalaci Node.js na provozních serverech. Toto je automaticky případ pro některé scénáře nasazení, jako je například Azure App Services, ale ne pro jiné, jako je Azure Service Fabric.
* Povolení `BuildServerSideRenderer` sestavení příznak způsobí, že vaše *node_modules* directory k publikování. Tato složka obsahuje 20 000 + soubory, které zvýší dobu nasazení.
* Ke spuštění kódu v prostředí Node.js, se nelze spoléhat na existenci specifické pro prohlížeč rozhraní API jazyka JavaScript, jako `window` nebo `localStorage`. Pokud váš kód (nebo některé knihovny třetí strany, který odkazujete) pokusí o pomocí těchto rozhraní API, budete při SSR dojde k chybě. Například nepoužívejte jQuery protože odkazuje na rozhraní API pro konkrétní prohlížeče na mnoha místech. Chcete-li zabránit chybám, musíte vyhnout SSR nebo Vyhněte se rozhraní API nebo knihovny specifické pro prohlížeč. Všechna volání do těchto rozhraní API můžete zabalit do kontroly pro zajištění, že nejsou vyvolány během SSR. Například v kódu jazyka JavaScript nebo TypeScript použijte kontrolu, jako je následující:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```
