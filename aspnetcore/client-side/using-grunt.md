---
title: Použití grunt v ASP.NET Core
author: rick-anderson
description: Použití grunt v ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: fc871e22f9bd5a9c137008f1d87019542c45b5d2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401763"
---
# <a name="use-grunt-in-aspnet-core"></a>Použití grunt v ASP.NET Core

Grunt je Spouštěč úloh JavaScriptu, který automatizuje skripty minifikace, kompilaci TypeScript, kvality kódu "Lint", pre-procesory šablon stylů CSS a stejně jako všechny opakující se Chore, které jsou potřeba k podpoře vývoje klienta. Grunt je plně podporovaný v aplikaci Visual Studio.

V tomto příkladu se jako výchozí bod používá prázdný projekt ASP.NET Core, který ukazuje, jak automatizovat proces sestavení klienta od začátku.

Příklad dokončení čistí cílový adresář nasazení, kombinuje soubory jazyka JavaScript, kontroluje kvalitu kódu, zúžení obsahu souboru JavaScriptu a nasadí do kořenového adresáře vaší webové aplikace. Budeme používat tyto balíčky:

* **grunt**: balíček Spouštěče úloh grunt.

* **grunt-contrib-Clean**: modul plug-in, který odebírá soubory nebo adresáře.

* **grunt-contrib-jshint**: modul plug-in, který kontroluje kvalitu kódu JavaScriptu.

* **grunt-contrib-Concat**: modul plug-in, který spojuje soubory do jednoho souboru.

* **grunt-contrib-uglify**: modul plug-in, který minifies JavaScript, aby se snížila velikost.

* **grunt-contrib-Watch**: modul plug-in, který sleduje aktivitu souboru.

## <a name="preparing-the-application"></a>Příprava aplikace

Začněte tím, že nastavíte novou prázdnou webovou aplikaci a přidáte ukázkové soubory TypeScript. Soubory TypeScript jsou automaticky zkompilovány do JavaScriptu pomocí výchozího nastavení sady Visual Studio a bude se jednat o náš nezpracovaný materiál pro zpracování pomocí grunt.

1. V aplikaci Visual Studio vytvořte nový `ASP.NET Web Application` .

2. V dialogovém okně **Nový projekt ASP.NET** vyberte ASP.NET Core **prázdnou** šablonu a klikněte na tlačítko OK.

3. V Průzkumník řešení zkontrolujte strukturu projektu. `\src`Složka obsahuje prázdné `wwwroot` a `Dependencies` uzly.

    ![prázdné webové řešení](using-grunt/_static/grunt-solution-explorer.png)

4. Přidejte novou složku s názvem `TypeScript` do adresáře projektu.

5. Před přidáním všech souborů se ujistěte, že má Visual Studio zaškrtnutou možnost kompilovat při uložení pro soubory TypeScript. Přejděte k **Tools**  >  **Možnosti**nástroje  >  **textový editor**  >  **Typescript**  >  **projekt**TypeScript:

    ![možnosti nastavení automatické kompilace souborů TypeScriptu](using-grunt/_static/typescript-options.png)

6. Klikněte pravým tlačítkem na `TypeScript` adresář a v místní nabídce vyberte **Přidat > nová položka** . Vyberte položku **souboru JavaScriptu** a pojmenujte soubor *chuti. TS* (Poznamenejte si \* příponu. TS). Zkopírujte řádek kódu TypeScript níže do souboru (při uložení se zobrazí nový soubor *Tastes.js* se zdrojem JavaScriptu).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Přidejte do adresáře **TypeScript** druhý soubor a pojmenujte ho `Food.ts` . Zkopírujte následující kód do souboru.

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

## <a name="configuring-npm"></a>Konfigurace NPM

Dále nakonfigurujte NPM tak, aby stahoval grunt a Grunt-Tasks.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na projekt a vyberte **přidat > novou položku** z kontextové nabídky. Vyberte položku **konfiguračního souboru npm** , ponechte výchozí název, *package.jsna*a klikněte na tlačítko **Přidat** .

2. V *package.jsv* souboru uvnitř `devDependencies` závorek objektu zadejte "grunt". Vyberte `grunt` ze seznamu IntelliSense a stiskněte klávesu ENTER. Sada Visual Studio nabídne název balíčku grunt a přidá dvojtečku. Napravo od dvojtečky vyberte nejnovější stabilní verzi balíčku z horní části seznamu technologie IntelliSense (stiskněte, `Ctrl-Space` Pokud se IntelliSense nezobrazuje).

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM používá [sémantickou správu verzí](https://semver.org/) k uspořádání závislostí. Sémantická verze, označovaná také jako SemVer, identifikuje balíčky se schématem číslování \<major> . \<minor> . \<patch> . Technologie IntelliSense zjednodušuje sémantickou správu verzí zobrazením pouze několika běžných možností. Horní položka v seznamu IntelliSense (0.4.5 v příkladu výše) je považována za nejnovější stabilní verzi balíčku. Symbol stříšky (^) odpovídá nejnovější hlavní verzi a znak tilda (~) odpovídá nejnovější dílčí verzi. Podívejte se na referenční informace o [analyzátoru npm semver verze](https://www.npmjs.com/package/semver) jako průvodce úplným expressivity, které poskytuje semver.

3. Přidejte další závislosti pro načtení grunt-contrib- \* Packages pro *Vyčištění*, *jshint*, *Concat*, *uglify*a *Sledujte* , jak je znázorněno v následujícím příkladu. Verze se nemusí shodovat s příkladem.

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

4. Uložte *package.jsdo* souboru.

Balíčky pro každou `devDependencies` položku budou staženy společně se všemi soubory, které každý balíček vyžaduje. Soubory balíčku můžete najít v adresáři *node_modules* tím, že v **Průzkumník řešení**povolíte tlačítko **Zobrazit všechny soubory** .

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Pokud potřebujete, můžete závislosti v **Průzkumník řešení** ručně obnovit kliknutím pravým tlačítkem na položku `Dependencies\NPM` a výběrem možnosti nabídky **obnovit balíčky** .

![obnovit balíčky](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurace grunt

Grunt je nakonfigurován pomocí manifestu s názvem *Gruntfile.js* , který definuje, načítá a registruje úlohy, které lze spustit ručně nebo které jsou nakonfigurovány pro automatické spouštění na základě událostí v aplikaci Visual Studio.

1. Klikněte pravým tlačítkem myši na projekt a vyberte možnost **Přidat**  >  **novou položku**. Vyberte šablonu položky **soubor JavaScriptu** , změňte název na *Gruntfile.js*a klikněte na tlačítko **Přidat** .

1. Přidejte následující kód pro *Gruntfile.js*. `initConfig`Funkce nastaví možnosti pro každý balíček a zbytek modulu načítá a registruje úlohy.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Uvnitř `initConfig` funkce přidejte možnosti pro `clean` úlohu, jak je znázorněno v příkladu *Gruntfile.js* níže. `clean`Úkol přijme pole řetězců adresáře. Tato úloha odebere soubory z *wwwroot/lib* a odebere celý adresář */TEMP* .

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Pod `initConfig` funkcí přidejte volání do `grunt.loadNpmTasks` . Tím se úkol spustitelný ze sady Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Uložte *Gruntfile.js*. Soubor by měl vypadat přibližně jako snímek obrazovky níže.

    ![počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Klikněte pravým tlačítkem na *Gruntfile.js* a v místní nabídce vyberte **Průzkumník Spouštěče úloh** . Otevře se okno **Průzkumník Spouštěče úloh** .

    ![Nabídka Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-menu.png)

1. Ověřte, že `clean` je v části **úlohy** v **Průzkumníkovi Spouštěče úloh**zobrazeno.

    ![seznam úkolů v Průzkumníkovi Spouštěče úloh](using-grunt/_static/task-runner-explorer-tasks.png)

1. Klikněte pravým tlačítkem na úlohu vyčistit a v místní nabídce vyberte **Spustit** . V příkazovém okně se zobrazuje průběh úkolu.

    ![Průzkumník Spouštěče úloh – spustit čištění úlohy](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Neexistují žádné soubory ani adresáře k vyčištění. Pokud chcete, můžete je ručně vytvořit v Průzkumník řešení a pak spustit čistou úlohu jako test.

1. Ve `initConfig` funkci přidejte položku pro `concat` použití kódu níže.

    `src`Pole vlastností seznam souborů, které se mají kombinovat, v pořadí, ve kterém by měly být kombinovány. `dest`Vlastnost přiřadí cestu k kombinovanému souboru, který je vytvořen.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all`Vlastnost ve výše uvedeném kódu je název cíle. Cíle se používají v některých úlohách grunt k povolení více prostředí sestavení. Můžete zobrazit předdefinované cíle pomocí technologie IntelliSense nebo přiřadit vlastní.

1. Přidejte `jshint` úlohu pomocí následujícího kódu.

    Nástroj jshint `code-quality` se spustí pro všechny soubory JavaScriptu nalezené v *dočasném* adresáři.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Možnost "-W069" je chyba vytvořená jshint, když JavaScript používá syntaxi závorky k přiřazení vlastnosti namísto zápisu tečky, tj. `Tastes["Sweet"]` místo `Tastes.Sweet` . Možnost vypne upozornění, aby zbytek procesu mohl pokračovat.

1. Přidejte `uglify` úlohu pomocí následujícího kódu.

    Úkol minifies soubor *combined.js* nalezen v dočasném adresáři a vytvoří soubor výsledku v wwwroot/lib po standardní konvenci pojmenování * \<file name\>.min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Pod voláním `grunt.loadNpmTasks` této zátěže `grunt-contrib-clean` Zahrňte stejné volání pro jshint, Concat a uglify pomocí níže uvedeného kódu.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Uložte *Gruntfile.js*. Soubor by měl vypadat nějak podobně jako v následujícím příkladu.

    ![Příklad úplného souboru grunt](using-grunt/_static/gruntfile-js-complete.png)

1. Všimněte si, že seznam úkolů v **Průzkumníkovi Spouštěče úloh** obsahuje `clean` `concat` úlohy, `jshint` a `uglify` . Spusťte jednotlivé úlohy v daném pořadí a sledujte výsledky v **Průzkumník řešení**. Každý úkol by měl běžet bez chyb.

    ![Průzkumník Spouštěče úloh spouští jednotlivé úlohy.](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Úkol Concat vytvoří nový soubor *combined.js* a umístí ho do dočasného adresáře. `jshint`Úloha se jednoduše spustí a nevyprodukuje výstup. `uglify`Úloha vytvoří nový soubor *combined.min.js* a umístí ho do *wwwroot/lib*. Po dokončení by řešení mělo vypadat přibližně jako snímek obrazovky níže:

    ![Průzkumník řešení po všech úlohách](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Další informace o možnostech jednotlivých balíčků najdete v části a v [https://www.npmjs.com/](https://www.npmjs.com/) poli hledání na hlavní stránce vyhledejte název balíčku. Můžete například vyhledat balíček grunt-contrib-Clean a získat odkaz na dokumentaci, který vysvětluje všechny jeho parametry.

### <a name="all-together-now"></a>Teď všechno dohromady

Použijte metodu grunt `registerTask()` ke spuštění řady úkolů v konkrétní sekvenci. Pokud například chcete spustit příklad výše uvedených kroků v pořadí vyčistit-> Concat-> jshint-> uglify, přidejte kód níže do modulu. Kód by měl být přidán na stejnou úroveň jako volání loadNpmTasks (), mimo initConfig.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Nový úkol se zobrazí v Průzkumníku Spouštěče úloh v části úkoly s aliasem. Můžete kliknout pravým tlačítkem a spustit stejným způsobem jako jiné úkoly. `all`Úloha se spustí `clean` , `concat` `jshint` a `uglify` v uvedeném pořadí.

![úkoly grunt aliasu](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Sledování změn

`watch`Úkol zachovává oči souborů a adresářů. Pokud se změny detekuje, sledování se automaticky aktivuje. Přidejte následující kód, který bude initConfig ke sledování změn \* souborů. js v adresáři TypeScript. Pokud dojde ke změně souboru JavaScriptu, `watch` úloha se spustí `all` .

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Přidejte volání k `loadNpmTasks()` zobrazení `watch` úlohy v Průzkumníku Spouštěče úloh.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

V Průzkumníku Spouštěče úloh klikněte pravým tlačítkem myši na úlohu kukátka a v místní nabídce vyberte spustit. Příkazové okno, které zobrazuje spuštěný úkol kukátka, zobrazí "čekání..." Zpráva. Otevřete jeden ze souborů TypeScriptu, přidejte mezeru a pak soubor uložte. Tím se aktivuje úloha kukátka a spustí se další úlohy, které se spustí v daném pořadí. Níže uvedený snímek obrazovky ukazuje vzorový běh.

![výstup spuštěných úloh](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Vytvoření vazby na události sady Visual Studio

Pokud nechcete spouštět úlohy ručně při každé práci v aplikaci Visual Studio, navažte úkoly na **před sestavením**, **po sestavení**, **Vyčištění**a otevřených událostech **projektu** .

Vytvoří vazby `watch` tak, že se spustí při každém otevření sady Visual Studio. V Průzkumníku Spouštěče úloh klikněte pravým tlačítkem myši na úlohu kukátka a vyberte projekt **vazby**  >  **otevřít** z místní nabídky.

![navázání úlohy k otevření projektu](using-grunt/_static/bindings-project-open.png)

Uvolněte a znovu načtěte projekt. Po opětovném načtení projektu se úloha kukátka začne automaticky spouštět.

## <a name="summary"></a>Souhrn

Grunt je výkonný Spouštěč úloh, který se dá použít k automatizaci většiny úloh sestavení klientů. Grunt využívá NPM k doručování svých balíčků a funkcí pro integraci nástrojů se sadou Visual Studio. Průzkumník Spouštěče úloh sady Visual Studio detekuje změny konfiguračních souborů a poskytuje praktické rozhraní pro spouštění úkolů, zobrazení spuštěných úloh a vázání úloh k událostem sady Visual Studio.
