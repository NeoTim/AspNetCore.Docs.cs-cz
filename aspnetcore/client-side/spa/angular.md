---
title: Použití šablony projektu Angular s jádrem ASP.NET
author: SteveSandersonMS
description: Přečtěte si, jak začít s šablonou projektu ASP.NET základní jednostránkové aplikace (SPA) pro úhlové a úhlové cli.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657631"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Použití šablony projektu Angular s jádrem ASP.NET

Aktualizovaná šablona projektu Angular poskytuje vhodný výchozí bod pro ASP.NET základní aplikace pomocí úhlového a úhlového rozhraní CLI k implementaci bohatého uživatelského rozhraní na straně klienta.

Šablona je ekvivalentní k vytvoření ASP.NET core projektu fungovat jako back-end rozhraní API a úhlové cli projekt fungovat jako uživatelské rozhraní. Šablona nabízí pohodlí hostování obou typů projektů v jednom projektu aplikace. V důsledku toho může být projekt aplikace sestaven a publikován jako jedna jednotka.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

Pokud máte nainstalovanou ASP.NET Core 2.1, není nutné instalovat šablonu projektu Angular.

Vytvořte nový projekt z příkazového `dotnet new angular` řádku pomocí příkazu v prázdném adresáři. Například následující příkazy vytvoří aplikaci v adresáři *moje nová aplikace* a přejdou do tohoto adresáře:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Spusťte aplikaci z Visual Studia nebo rozhraní .NET Core CLI:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

Otevřete vygenerovaný soubor *.csproj* a spusťte aplikaci jako obvykle.

Proces sestavení obnoví npm závislosti na první spuštění, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli/)

Ujistěte se, že `ASPNETCORE_Environment` máte proměnnou prostředí volanou s hodnotou `Development`. Ve Windows (v výzvách, které `SET ASPNETCORE_Environment=Development`nejsou powershelly), spusťte . Na Linuxu nebo macOS spusťte `export ASPNETCORE_Environment=Development`.

Spusťte [dotnet sestavení](/dotnet/core/tools/dotnet-build) ověřit sestavení aplikace správně. Při prvním spuštění proces sestavení obnoví npm závislosti, což může trvat několik minut. Následná sestavení jsou mnohem rychlejší.

Spuštění [montovny dotnet](/dotnet/core/tools/dotnet-run) spusťte. Je zaznamenána zpráva podobná následující:

```console
Now listening on: http://localhost:<port>
```

Přejděte na tuto adresu URL v prohlížeči.

> [!WARNING]
> Aplikace spustí instanci úhlového cli serveru na pozadí. Zpráva podobná následující je zaznamenána: *NG Live Development Server&lt;poslouchá&gt;na localhost: http://localhost:&ltotherport&gt;, otevřete prohlížeč ;otherport*. Ignorovat tuto&mdash;zprávu **není** adresa URL pro kombinované ASP.NET core a úhlové CLI aplikace.

---

Šablona projektu vytvoří aplikaci ASP.NET Core a aplikaci Angular. Aplikace ASP.NET Core je určena pro přístup k datům, autorizaci a další problémy na straně serveru. Angular aplikace, s bydlištěm v podadresáři *ClientApp,* je určen pro všechny problémy s rozhraním.

## <a name="add-pages-images-styles-modules-etc"></a>Přidejte stránky, obrázky, styly, moduly atd.

Adresář *ClientApp* obsahuje standardní aplikaci Úhlové cli. Další informace naleznete v oficiální [dokumentaci angular.](https://angular.io)

Existují mírné rozdíly mezi aplikací Angular vytvořenou touto šablonou a aplikací vytvořenou samotným úhlovým CLI (přes); `ng new` možnosti aplikace se však nemění. Aplikace vytvořená šablonou obsahuje rozložení založené na [Bootstrapu](https://getbootstrap.com/)a základní příklad směrování.

## <a name="run-ng-commands"></a>Spuštění příkazů ng

V příkazovém řádku přepněte do podadresáře *ClientApp:*

```console
cd ClientApp
```

Pokud máte `ng` nástroj nainstalován globálně, můžete spustit některý z jeho příkazů. Můžete například spustit `ng lint` `ng test`, nebo některý z dalších [příkazů úhlového příkazu cli](https://angular.io/cli). Není však třeba `ng serve` spouštět, protože vaše aplikace ASP.NET Core se zabývá zobrazováním částí aplikace na straně serveru i na straně klienta. Interně se `ng serve` používá ve vývoji.

Pokud nástroj nemáte nainstalovaný, `ng` spusťte `npm run ng` jej. Můžete například spustit `npm run ng lint` `npm run ng test`nebo .

## <a name="install-npm-packages"></a>Instalace balíčků npm

Chcete-li nainstalovat balíčky npm od jiných výrobců, použijte příkazový řádek v podadresáři *ClientApp.* Příklad:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikování a nasazení

Ve vývoji aplikace běží v režimu optimalizovaném pro vývojáře. Například javascriptové balíčky obsahují zdrojové mapy (takže při ladění můžete vidět původní kód TypeScript). Aplikace sleduje změny souborů TypeScript, HTML a CSS na disku a automaticky se znovu zkompiluje a znovu načte, když vidí, že se tyto soubory mění.

V produkčním prostředí můžete poskytovat verzi aplikace optimalizovanou pro výkon. Tato možnost je nakonfigurována tak, aby se stala automaticky. Při publikování konfigurace sestavení vyzařuje minified, ahead-of-time (AoT) zkompilované sestavení kódu na straně klienta. Na rozdíl od vývojového sestavení nevyžaduje produkční sestavení instalaci node.js na server (pokud jste nepovolili vykreslování na straně serveru (SSR)).

Můžete použít standardní [ASP.NET core hosting a metody nasazení](xref:host-and-deploy/index).

## <a name="run-ng-serve-independently"></a>Spustit "ng sloužit" nezávisle

Projekt je nakonfigurován tak, aby spustil vlastní instanci serveru Úhlové cli na pozadí při spuštění aplikace ASP.NET Core v režimu vývoje. To je výhodné, protože není nutné spustit samostatný server ručně.

Toto výchozí nastavení má nevýhodu. Pokaždé, když upravíte kód C# a aplikace ASP.NET Core musí restartovat, restartuje se server Úhlové cli. Pro spuštění zálohy je zapotřebí přibližně 10 sekund. Pokud provádíte časté úpravy kódu jazyka C# a nechcete čekat na restartování úhlového cli, spusťte server angular CLI externě, nezávisle na procesu ASP.NET Core. Postupujte následovně:

1. V příkazovém řádku přepněte do podadresáře *ClientApp* a spusťte vývojový server Úhlové rozhraní příkazového řádku:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Slouží `npm start` ke spuštění úhlového cli `ng serve`vývojový server, nikoli , tak, aby konfigurace v *package.json* je respektována. Chcete-li předat další parametry serveru úhlového rozhraní `scripts` CLI, přidejte je do příslušného řádku v souboru *package.json.*

2. Upravte aplikaci ASP.NET Core tak, aby místo spuštění vlastní instance úhlového cli používala externí instanci úhlového cli. Ve třídě *Startup* nahraďte `spa.UseAngularCliServer` vyvolání následujícím:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Když spustíte aplikaci ASP.NET Core, nespustí se server úhlového cli. Místo toho se použije instance, kterou jste spustili ručně. To umožňuje rychlejší spuštění a restartování. Už nečeká na úhlové cli znovu sestavit klientskou aplikaci pokaždé.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Předání dat z kódu .NET do kódu Typu Typu Script

Během SSR můžete chtít předat data na žádost z aplikace ASP.NET Core do aplikace Angular. Můžete například předat informace o souborech cookie nebo něco přečteného z databáze. Chcete-li to provést, upravte třídu *Po spuštění.* V zpětném `UseSpaPrerendering`volání pro nastavte `options.SupplyData` hodnotu, například následující:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

Zpětné `SupplyData` volání umožňuje předat libovolná data json-serializovatelná data (například řetězce, logické hodnoty nebo čísla). Váš kód *main.server.ts* obdrží `params.data`toto jako . Například předchozí ukázka kódu předá logickou hodnotu jako `params.data.isHttpsRequest` do zpětného `createServerRenderer` volání. Můžete předat do jiných částí aplikace v libovolném způsobem podporované Angular. Například podívejte se, jak *main.server.ts* předá hodnotu `BASE_URL` jakékoli součásti, jejíž konstruktor je deklarován k jeho přijetí.

### <a name="drawbacks-of-ssr"></a>Nevýhody SSR

Ne všechny aplikace mají prospěch z SSR. Hlavním přínosem je vnímaný výkon. Návštěvníci, kteří se k vaší aplikaci dostanou prostředpou prostředpou k pomalému síťovému připojení nebo na pomalých mobilních zařízeních, uvidí počáteční ui rychle, i když trvá nějakou dobu, než načte nebo analyzují balíčky JavaScriptu. Nicméně, mnoho SA se používá hlavně přes rychlé, interní firemní sítě na rychlých počítačích, kde se aplikace objeví téměř okamžitě.

Současně existují významné nevýhody povolení SSR. To zvyšuje složitost procesu vývoje. Váš kód musí běžet ve dvou různých prostředích: na straně klienta a na straně serveru (v prostředí Node.js vyvolaném z ASP.NET Core). Zde je několik věcí, které je třeba mít na paměti:

* SSR vyžaduje instalaci Node.js na produkčních serverech. To je automaticky případ pro některé scénáře nasazení, jako je například Azure App Services, ale ne pro jiné, jako je například Azure Service Fabric.
* Povolení `BuildServerSideRenderer` příznaku sestavení způsobí publikování *node_modules* adresáře. Tato složka obsahuje více než 20 000 souborů, což zvyšuje dobu nasazení.
* Chcete-li spustit kód v prostředí Node.js, nemůže se spoléhat na existenci javascriptových api specifických pro prohlížeč, jako `window` jsou nebo `localStorage`. Pokud váš kód (nebo některé knihovny třetích stran, na které odkazujete) se pokusí použít tato rozhraní API, zobrazí se chyba během SSR. Například nepoužívejte jQuery, protože odkazuje na prohlížeče specifické api na mnoha místech. Chcete-li zabránit chybám, musíte se buď vyhnout SSR nebo se vyhnout prohlížeče specifické pro api nebo knihovny. Můžete zabalit všechna volání těchto api v kontrolách zajistit, že nejsou vyvolány během SSR. V kódu Jazyka JavaScript nebo TypeScript použijte například následující:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/identity/spa>
