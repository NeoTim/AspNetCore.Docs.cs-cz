---
title: Použití Grunt v ASP.NET Core
author: rick-anderson
description: Použití Grunt v ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657589"
---
# <a name="use-grunt-in-aspnet-core"></a>Použití Grunt v ASP.NET Core

Grunt je javascriptový plánovač úloh, který automatizuje minifikaci skriptů, kompilaci TypeScript, nástroje pro kvalitu kódu "lint", předprocesory CSS a téměř jakoukoli opakující se práci, která potřebuje dělat, aby podpořila vývoj klientů. Grunt je plně podporován v sadě Visual Studio.

Tento příklad používá prázdný ASP.NET core projektu jako výchozí bod, ukázat, jak automatizovat proces sestavení klienta od začátku.

Hotový příklad čistí cílový adresář nasazení, kombinuje soubory JavaScriptu, kontroluje kvalitu kódu, kondenzuje obsah souboru JavaScript a nasazuje se do kořenového adresáře webové aplikace. Použijeme následující balíčky:

* **grunt**: Grunt úkol běžec balíček.

* **grunt-contrib-clean**: Plugin, který odstraňuje soubory nebo adresáře.

* **grunt-contrib-jshint**: plugin, který rekapituluje kvalitu kódu JavaScriptu.

* **grunt-contrib-concat**: Plugin, který spojuje soubory do jednoho souboru.

* **grunt-contrib-uglify**: Plugin, který minifies JavaScript ke snížení velikosti.

* **grunt-contrib-watch**: Plugin, který sleduje aktivitu souborů.

## <a name="preparing-the-application"></a>Příprava aplikace

Chcete-li začít, nastavte novou prázdnou webovou aplikaci a přidejte ukázkové soubory jazyka TypeScript. Soubory TypeScript ukládají se automaticky do JavaScriptu pomocí výchozího nastavení sady Visual Studio a budou naší surovinou pro zpracování pomocí Grunt.

1. V sadě Visual Studio `ASP.NET Web Application`vytvořte nový .

2. V dialogovém **okně Nový ASP.NET projekt** vyberte šablonu ASP.NET Jádro **prázdné** a klepněte na tlačítko OK.

3. V Průzkumníku řešení zkontrolujte strukturu projektu. Složka `\src` obsahuje `wwwroot` prázdné `Dependencies` a uzly.

    ![prázdné webové řešení](using-grunt/_static/grunt-solution-explorer.png)

4. Přidejte do `TypeScript` adresáře projektu novou složku s názvem.

5. Před přidáním souborů se ujistěte, že visual studio má možnost "kompilace při uložení" pro soubory Typu Script zaškrtnuta. Přejděte na**možnosti** >  **nástrojů** > **Textový editor** > **typescript** > **projektu**:

    ![volby nastavení automatické kompilace souborů jazyka TypeScript](using-grunt/_static/typescript-options.png)

6. Klepněte pravým `TypeScript` tlačítkem myši na adresář a z kontextové nabídky vyberte **Přidat > novou položku.** Vyberte položku **souboru JavaScript** a pojmenujte \*soubor *Tastes.ts* (všimněte si přípony .ts). Zkopírujte řádek kódu TypeScript u níže do souboru (při uložení se se zdrojem JavaScriptu zobrazí nový soubor *Tastes.js).*

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Přidejte druhý soubor do **adresáře TypeScript** a pojmenujte jej `Food.ts`. Zkopírujte níže uvedený kód do souboru.

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>Konfigurace npm

Dále nakonfigurujte NPM ke stažení grunt a grunt-úkoly.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a z kontextové nabídky vyberte **Přidat > novou položku.** Vyberte položku **konfiguračního souboru NPM,** ponechte výchozí název *package.json*a klepněte na tlačítko **Přidat.**

2. V souboru *package.json* `devDependencies` zadejte uvnitř závorek objektu "grunt". Vyberte `grunt` ze seznamu Intellisense a stiskněte klávesu Enter. Visual Studio bude citovat grunt název balíčku a přidat dvojtečku. Napravo od dvojtečky vyberte nejnovější stabilní verzi balíčku z horní části seznamu `Ctrl-Space` Intellisense (stiskněte, pokud se nezobrazí Intellisense).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM používá [sémantické správu verzí](https://semver.org/) k uspořádání závislostí. Sémantické správu verzí, označované také jako SemVer, identifikuje balíčky s schématem \<číslování hlavní>. \<menší>. \<> náplasti. Technologie Intellisense zjednodušuje sémantickou správu verzí tím, že zobrazuje pouze několik běžných možností. Horní položka v seznamu Intellisense (0.4.5 v příkladu výše) je považována za nejnovější stabilní verzi balíčku. Symbol stříšky (^) odpovídá nejnovější hlavní verzi a vlnovce (~) odpovídá nejnovější dílčí verzi. Podívejte se na [npm semver verze analyzátor odkaz](https://www.npmjs.com/package/semver) jako vodítko k plné expresivity, které poskytuje SemVer.

3. Přidejte další závislosti pro načtení balíčků\* grunt-contrib- pro *clean*, *jshint*, *concat*, *uglify*a *sledujte,* jak je znázorněno v příkladu níže. Verze nemusí odpovídat příkladu.

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. Uložte soubor *package.json.*

Balíčky pro `devDependencies` každou položku se stáhnou spolu se všemi soubory, které každý balíček vyžaduje. Soubory balíčků můžete najít v *node_modules* adresáři povolením tlačítka **Zobrazit všechny soubory** v **Průzkumníku řešení**.

![chrochtat node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Pokud potřebujete, můžete ručně obnovit závislosti v **Průzkumníku řešení** `Dependencies\NPM` kliknutím pravým tlačítkem myši na a výběrem možnosti nabídky **Obnovit balíčky.**

![obnovení balíčků](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurace Grunt

Grunt je konfigurován pomocí manifestu s názvem *Gruntfile.js,* který definuje, načte a registruje úlohy, které lze spustit ručně nebo nakonfigurovat tak, aby se spouštěly automaticky na základě událostí v sadě Visual Studio.

1. Klepněte pravým tlačítkem myši na projekt a vyberte **přidat** > **novou položku**. Vyberte šablonu **položky souboru JavaScriptu,** změňte název na *Gruntfile.js*a klepněte na tlačítko **Přidat.**

1. Přidejte následující kód do *souboru Gruntfile.js*. Funkce `initConfig` nastaví možnosti pro každý balíček a zbytek modulu načte a zaregistruje úkoly.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Uvnitř `initConfig` funkce přidejte možnosti pro úlohu, `clean` jak je znázorněno v příkladu *Gruntfile.js* níže. Úloha `clean` přijímá pole adresářových řetězců. Tato úloha odebere soubory z *wwwroot/lib* a odebere celý *adresář /temp.*

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Pod `initConfig` funkci přidejte volání `grunt.loadNpmTasks`do aplikace . Tím bude úloha spustitelné z visual studia.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Uložit *Gruntfile.js*. Soubor by měl vypadat podobně jako snímek obrazovky níže.

    ![počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Klikněte pravým tlačítkem myši na *soubor Gruntfile.js* a z kontextové nabídky vyberte **Průzkumníka průzkumníka úloh.** Otevře se okno **Průzkumníka plánovače úloh.**

    ![nabídka průzkumníka posuzujícíúlohy úloh](using-grunt/_static/task-runner-explorer-menu.png)

1. Ověřte, že `clean` se zobrazuje v části **Úkoly** v **Průzkumníku průzkumníka plánovače úloh**.

    ![seznam úloh průzkumníka posuzování úloh](using-grunt/_static/task-runner-explorer-tasks.png)

1. Klepněte pravým tlačítkem myši na čistou úlohu a z kontextové nabídky vyberte **Spustit.** Příkazové okno zobrazuje průběh úkolu.

    ![průzkumník úloh spustit čistou úlohu](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Zatím nejsou k dispozici žádné soubory ani adresáře, které by bylo možné vyčistit. Pokud chcete, můžete je ručně vytvořit v Průzkumníku řešení a potom spustit čistou úlohu jako test.

1. Do `initConfig` funkce přidejte položku pro `concat` použití níže uvedeného kódu.

    Pole `src` vlastností obsahuje seznam souborů, které mají být kombinovány, v pořadí, v jakém by měly být kombinovány. Vlastnost `dest` přiřadí cestu k kombinovanému souboru, který je vytvořen.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Vlastnost `all` ve výše uvedeném kódu je název cíle. Cíle se používají v některých úlohách Grunt povolit více prostředí sestavení. Integrované cíle můžete zobrazit pomocí technologie IntelliSense nebo přiřadit vlastní.

1. Přidejte `jshint` úkol pomocí níže uvedeného kódu.

    Nástroj jshint `code-quality` je spuštěn proti každému souboru JavaScriptu, který se nachází v *dočasném* adresáři.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Možnost "-W069" je chyba vytvořená jshintem, když JavaScript používá syntaxi závorek `Tastes["Sweet"]` k `Tastes.Sweet`přiřazení vlastnosti namísto tečkového zápisu, tj. Tato možnost vypne upozornění, aby zbytek procesu mohl pokračovat.

1. Přidejte `uglify` úkol pomocí níže uvedeného kódu.

    Úloha minifikuje soubor *combined.js* nalezený v dočasném adresáři a vytvoří výsledný soubor v souboru wwwroot/lib podle standardního * \<názvu\>souboru konvence _min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Pod volání, `grunt.loadNpmTasks` které `grunt-contrib-clean`načte , zahrnout stejné volání pro jshint, concat a uglify pomocí níže uvedeného kódu.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Uložit *Gruntfile.js*. Soubor by měl vypadat podobně jako v níže uvedeném příkladu.

    ![kompletní příklad grunt souboru](using-grunt/_static/gruntfile-js-complete.png)

1. Všimněte si, že seznam `clean` `concat`Úlohy `uglify` **průzkumníka plánovače úloh** obsahuje aplikace , `jshint` a úkoly. Spusťte každou úlohu v pořadí a sledujte výsledky v **Průzkumníku řešení**. Každá úloha by měla být spuštěna bez chyb.

    ![Průzkumník průzkumníka úloh spustit každou úlohu](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Úloha concat vytvoří nový soubor *combined.js* a umístí jej do dočasného adresáře. Úloha `jshint` se jednoduše spustí a nevytváří výstup. Úloha `uglify` vytvoří nový soubor *combined.min.js* a umístí jej do *wwwroot/lib*. Po dokončení by řešení mělo vypadat podobně jako na následujícím snímku obrazovky:

    ![průzkumník řešení po všech úkolech](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Další informace o možnostech každého [https://www.npmjs.com/](https://www.npmjs.com/) balíčku naleznete a vyhledejte název balíčku ve vyhledávacím poli na hlavní stránce. Můžete například vyhledat balíček grunt-contrib-clean a získat odkaz na dokumentaci, který vysvětluje všechny jeho parametry.

### <a name="all-together-now"></a>Teď všechno dohromady

Pomocí metody Grunt `registerTask()` spusťte řadu úloh v určitém pořadí. Chcete-li například spustit výše uvedené příkladové kroky v pořadí clean -> concat -> jshint -> uglify, přidejte níže uvedený kód do modulu. Kód by měl být přidán na stejnou úroveň jako volání loadNpmTasks() mimo initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Nový úkol se zobrazí v Průzkumníkovi úloh v části Alias úkoly. Můžete klepnout pravým tlačítkem myši a spustit jej stejně jako ostatní úkoly. Úloha `all` bude `clean` `concat`spuštěna `uglify`v `jshint` pořadí .

![alias grunt úkoly](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Sledování změn

Úkol `watch` sleduje soubory a adresáře. Hodinky spustí úlohy automaticky, pokud zjistí změny. Přidejte níže uvedený kód do initConfig a sledujte změny souborů \*JS v adresáři TypeScript. Pokud dojde ke změně `watch` souboru `all` JavaScriptu, spustí se úloha.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Přidáním volání `loadNpmTasks()` zobrazíte `watch` úkol v Průzkumníkovi úloh.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Klikněte pravým tlačítkem myši na úlohu sledování v Průzkumníkovi běhu úloh a z kontextové nabídky vyberte Spustit. Ve příkazovém okně, které zobrazuje spuštěnou úlohu sledování, se zobrazí "Čekání..." Zprávu. Otevřete jeden ze souborů TypeScriptu, přidejte mezeru a uložte soubor. Tím se spustí úloha sledování a ostatní úlohy spustit v pořadí. Snímek obrazovky níže ukazuje ukázkový běh.

![spuštění výstupu úloh](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Vazba na události sady Visual Studio

Pokud nechcete ručně spustit úkoly při každé práci v sadě Visual Studio, svázat úkoly **před sestavením**, **po sestavení**, **vyčistit**a **otevřít projekt** události.

Bind `watch` tak, aby se spustí při každém otevření sady Visual Studio. V Průzkumníku úloh y klikněte pravým tlačítkem myši na úkol sledování a z kontextové nabídky vyberte **možnost Vazby** > **projektu Otevřít.**

![svázat úkol s otevřením projektu](using-grunt/_static/bindings-project-open.png)

Uvolnění a opětovné načtení projektu. Při znovunačtení projektu se úloha sledování spustí automaticky.

## <a name="summary"></a>Souhrn

Grunt je výkonný plánovač úloh, který lze použít k automatizaci většiny úloh sestavení klienta. Grunt využívá NPM k doručování svých balíčků a nabízí integraci nástrojů s Visual Studio. Průzkumník Průzkumník úloh sady Visual Studio detekuje změny konfiguračních souborů a poskytuje pohodlné rozhraní pro spouštění úloh, zobrazení spuštěných úloh a svázání úloh s událostmi sady Visual Studio.
