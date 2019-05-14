---
title: Použití nástroje Grunt v ASP.NET Core
author: rick-anderson
description: Použití nástroje Grunt v ASP.NET Core
ms.author: riande
ms.date: 05/10/2019
uid: client-side/using-grunt
ms.openlocfilehash: 718a1358c0474711b05bb2c90dc86ec9edacbf1e
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610219"
---
# <a name="use-grunt-in-aspnet-core"></a>Použití nástroje Grunt v ASP.NET Core

Podle [Noel rýže](https://blog.falafel.com/falafel-software-recognized-sitefinity-website-year/)

Grunt je Spouštěče úloh JavaScriptu, který automatizuje skript připravenost k minifikaci, TypeScript kompilace, nástroji "lint" kvality kódu, šablony stylů CSS předběžné procesory a téměř všechny opakované případě vyžadující způsobem pro podporu vývoje klienta. Grunt je plně podporováno v sadě Visual Studio.

Tento příklad používá prázdný projekt ASP.NET Core jako výchozí bod, jak automatizovat proces sestavení klienta od začátku.

Dokončení příkladu vyčistí cílový adresář nasazení, kombinuje Javascriptové soubory, zkontroluje kvalitu kódu, zestruční obsah souboru jazyka JavaScript a nasadí do kořenového adresáře webové aplikace. Použijeme následující balíčky:

* **grunt**: Balíček nástroje Grunt úloh runner.

* **grunt-contrib-clean**: Modul plug-in, který odstraní soubory nebo adresáře.

* **grunt-contrib-jshint**: Modul plug-in kontroly kvality kódu jazyka JavaScript.

* **grunt-contrib-concat**: Modul plug-in, který spojuje soubory do jediného souboru.

* **grunt-contrib-uglify**: Modul plug-in minifikuje zmenšit velikost kódu jazyka JavaScript.

* **grunt-contrib-watch**: Modul plug-in, které sleduje aktivity soubor.

## <a name="preparing-the-application"></a>Příprava aplikace

Pokud chcete začít, nastavte novou prázdnou webovou aplikaci a přidejte soubory TypeScript příklad. Soubory TypeScript automaticky kompilovány do jazyka JavaScript pomocí výchozího nastavení sady Visual Studio a bude náš suroviny proces použití nástroje Grunt.

1. V sadě Visual Studio vytvořte nový `ASP.NET Web Application`.

2. V **nový projekt ASP.NET** dialogového okna, vyberte ASP.NET Core **prázdný** šablonu a klikněte na tlačítko OK.

3. V Průzkumníku řešení zkontrolujte strukturu projektu. `\src` Složka obsahuje prázdný `wwwroot` a `Dependencies` uzly.

    ![prázdný webový řešení](using-grunt/_static/grunt-solution-explorer.png)

4. Přidat novou složku s názvem `TypeScript` k adresáři projektu.

5. Před přidáním všech souborů, ujistěte se, že Visual Studio poskytuje možnost "zkompilovat při uložení" pro TypeScript soubory se změnami. Přejděte do **nástroje** > **možnosti** > **textový Editor** > **Typescript**  >  **Projektu**:

    ![Možnosti nastavení automatické kompilaci souborů TypeScript](using-grunt/_static/typescript-options.png)

6. Klikněte pravým tlačítkem myši `TypeScript` adresář a zaškrtnout možnost **Přidat > Nová položka** v místní nabídce. Vyberte **soubor JavaScript** položku a zadejte název souboru *Tastes.ts* (Poznámka: \*.ts rozšíření). Zkopírovat řádek níže uvedený kód TypeScript do souboru (po uložení, nový *Tastes.js* souboru se zobrazí s zdroji JavaScript).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Přidat druhý soubor **TypeScript** adresáře a pojmenujte ho `Food.ts`. Zkopírujte následující kód do souboru.

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

V dalším kroku nakonfigurujte NPM pro stažení nástroje grunt a grunt úlohy.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt a vyberte **Přidat > Nová položka** v místní nabídce. Vyberte **konfigurační soubor NPM** položky, ponechte výchozí název *package.json*a klikněte na tlačítko **přidat** tlačítko.

2. V *package.json* uvnitř souboru `devDependencies` objektu složené závorky, zadejte "grunt". Vyberte `grunt` z technologie Intellisense seznam a stiskněte klávesu Enter. Visual Studio se nabídka grunt název balíčku a přidejte dvojtečku. Napravo od dvojtečku, vyberte nejnovější stabilní verze balíčku z horní části seznamu technologie Intellisense (stiskněte `Ctrl-Space` Pokud technologie Intellisense se nezobrazí).

    ![grunt technologie Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM používá [sémantické správy verzí](http://semver.org/) k uspořádání závislosti. Sémantické správy verzí, označované také jako SemVer identifikuje balíčky se schéma číslování \<hlavní >.\< podverze >. \<opravy >. Technologie IntelliSense zjednodušuje tím, že zobrazuje pouze několik běžné volby sémantické správy verzí. Horní položku v seznamu technologie Intellisense (0.4.5 v příkladu výše) se považuje za nejnovější stabilní verze balíčku. Symbol stříšky (^) odpovídá nejnovější hlavní verzi a tilda (~) odpovídá nejnovější dílčí verzi. Najdete v článku [odkaz analyzátoru verzi semver NPM](https://www.npmjs.com/package/semver) jako průvodce k úplné expressivity poskytující SemVer.

3. Přidat další závislosti načíst grunt-contrib -\* balíčky *čisté*, *jshint*, *concat*, *uglify*a *watch* jak je znázorněno v následujícím příkladu. Verze nemusejí být stejné v příkladu.

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

4. Uložit *package.json* souboru.

Balíčky pro každou položku devDependencies stáhne společně s všechny soubory, které vyžaduje každý balíček. Můžete najít soubory v balíčku `node_modules` adresáře tím, že **zobrazit všechny soubory** tlačítko v Průzkumníku řešení.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Pokud je potřeba je ručně obnovit závislosti v Průzkumníkovi řešení pravým tlačítkem myši na `Dependencies\NPM` a vyberete **obnovit balíčky** nabídky.

![Obnovení balíčků](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurace nástroje Grunt

Grunt je nakonfigurovaný nástrojem manifestu s názvem *soubor Gruntfile.js* , který definuje, načítá a registruje úlohy, které lze spustit ručně nebo konfigurované pro běh automaticky na základě událostí v sadě Visual Studio.

1. Klikněte pravým tlačítkem na projekt a vyberte **Přidat > Nová položka**. Vyberte **Grunt konfigurační soubor** možnost, ponechte výchozí název *soubor Gruntfile.js*a klikněte na tlačítko **přidat** tlačítko.

   Počáteční kód obsahuje definici modulu a `grunt.initConfig()` metoda. `initConfig()` Slouží k nastavení možností pro každý balíček a zbytek modulu se načtou a zaregistrovat úlohy.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

2. Uvnitř `initConfig()` metody přidat možnosti pro `clean` úloh, jak je znázorněno v příkladu *soubor Gruntfile.js* níže. Úkolu vyčisti přijímá pole řetězců adresářů. Tento úkol odstraní soubory z wwwroot/lib a odstraní celý/dočasný adresář.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

3. Pod initConfig() metodu, přidejte volání do `grunt.loadNpmTasks()`. To způsobí, že úloha spustitelných ze sady Visual Studio.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

4. Uložit *soubor Gruntfile.js*. Soubor by měl vypadat přibližně jako na následujícím snímku obrazovky.

    ![Počáteční gruntfile](using-grunt/_static/gruntfile-js-initial.png)

5. Klikněte pravým tlačítkem na *soubor Gruntfile.js* a vyberte **Task Runner Explorer** v místní nabídce. Otevře se okno Task Runner Explorer.

    ![Nabídka Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-menu.png)

6. Ověřte, že `clean` zobrazí v části **úlohy** v Task Runner Explorer.

    ![Seznam úkolů Průzkumníka Spouštěče úloh](using-grunt/_static/task-runner-explorer-tasks.png)

7. Klikněte pravým tlačítkem na úkolu vyčisti a vyberte **spustit** v místní nabídce. Příkazové okno zobrazí průběh úlohy.

    ![runner explorer spusťte čisté úkol](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Neexistují žádné soubory nebo adresáře zatím vyčistit. Pokud chcete můžete je vytvořit ručně v Průzkumníku řešení a potom spustit úkolu vyčisti jako test.

8. V metodě initConfig() přidat položku pro `concat` pomocí níže uvedeného kódu.

    `src` Pole vlastnosti obsahuje soubory zkombinovat v pořadí, by měly být kombinované. `dest` Vlastnost přiřadí cestu k souboru, který je vytvořen kombinované.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all` Vlastnost ve výše uvedeném kódu je název cíle. Cíle se používají v některých úkolů Grunt umožňuje prostředí s více sestavení. Můžete zobrazit předdefinované cíle pomocí technologie Intellisense nebo přiřadit vlastní.

9. Přidat `jshint` úloh, pomocí níže uvedeného kódu.

    Spustí nástroj jshint kvality kódu pro každý soubor JavaScript najde v adresáři temp.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Možnost "-W069" chybu vytvořil jshint při párování JavaScript používá syntaxi přiřazení vlastnosti namísto zápisu s tečkou, to znamená `Tastes["Sweet"]` místo `Tastes.Sweet`. Možnost vypne upozornění umožňující zbytek procesu pokračovat.

10. Přidat `uglify` úloh, pomocí níže uvedeného kódu.

    Minifikuje úlohy *combined.js* soubor najde v adresáři temp a vytvoří soubor s výsledky v wwwroot/lib podle standardní konvence  *\<název_souboru\>. min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

11. V části grunt.loadNpmTasks() volání, která načte vyčistit contrib grunt patří stejné volání pro jshint, funkce concat a uglify pomocí níže uvedeného kódu.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

12. Uložit *soubor Gruntfile.js*. Soubor by měl vypadat přibližně jako v příkladu níže.

    ![Příklad souboru kompletní grunt](using-grunt/_static/gruntfile-js-complete.png)

13. Všimněte si, že se seznamu úkolů Průzkumníka Spouštěče úloh zahrnuje `clean`, `concat`, `jshint` a `uglify` úlohy. Spusťte každý úkol v pořadí a sledujte výsledky v Průzkumníku řešení. Každý úkol by měl spustit bez chyb.

    ![Průzkumník Spouštěče úloh spouštět každý úkol](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Vytvoří nový úkol concat *combined.js* soubor a umístí ji do dočasného adresáře. Úloha jshint jednoduše spustí a nevytvoří výstup. Vytvoří nový úkol uglify *combined.min.js* soubor a umístí jej do wwwroot/lib. Po dokončení, řešení by mělo vypadat jako na následujícím snímku obrazovky:

    ![Průzkumník řešení po všech úloh.](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Další informace o možnostech pro každý balíček [ https://www.npmjs.com/ ](https://www.npmjs.com/) a vyhledávací název balíčku do vyhledávacího pole na hlavní stránce. Můžete například vyhledat balíček grunt contrib vyčistit získat odkaz na dokumentaci, který vysvětluje všechny její parametry.

### <a name="all-together-now"></a>Teď všechno dohromady

Použít Grunt `registerTask()` způsob spuštění řadu úkolů v určitém pořadí. Například pro spuštění v příkladu výše uvedené kroky v pořadí, vyčistit -> concat -> jshint -> uglify, přidejte následující kód do modulu. Na stejné úrovni jako loadNpmTasks() volání mimo initConfig, měli byste přidat kód.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Nová úloha se zobrazí v Task Runner Explorer v části úlohy Alias. Pravým tlačítkem a jej spustit stejně jako další úkoly. `all` Úloha poběží `clean`, `concat`, `jshint` a `uglify`, v pořadí.

![úlohy alias grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Sledování změn

A `watch` úkolů udržuje přehled o soubory a adresáře. Hodinkami aktivuje úlohy automaticky, pokud zjistí změny. Přidejte následující kód k initConfig a sledujte změny \*soubory JS v adresáři TypeScript. Pokud změníte soubor jazyka JavaScript, `watch` poběží `all` úloh.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Přidejte volání do `loadNpmTasks()` zobrazíte `watch` úkol v Task Runner Explorer.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Klikněte pravým tlačítkem na sledování úkolů v Task Runner Explorer a v místní nabídce vyberte příkaz spustit. Příkazové okno zobrazující sledování úloha spuštěná se zobrazí "Čekání..." zpráva. Otevřete jeden ze souborů TypeScript, přidejte mezeru a pak soubor uložte. To provede aktivaci úlohy sledování a aktivuje dalších úkolů ke spuštění v pořadí. Následující snímek obrazovky ukazuje ukázkové spuštění.

![spuštění výstupu úlohy](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Vazba na Visual Studio události

Pokud chcete ručně spustit vaše úlohy pokaždé, když pracujete v sadě Visual Studio, můžete vytvořit vazbu na úloh **před sestavení**, **po sestavení**, **Vyčistit**, a  **Projekt Open** události.

Pojďme vytvořit vazbu mezi `watch` tak, aby se spustí pokaždé, když se otevře v sadě Visual Studio. V Task Runner Explorer, klikněte pravým tlačítkem na sledování úkolů a vyberte **vazby > Otevřít projekt** v místní nabídce.

![vázat úlohy k otevření projektu](using-grunt/_static/bindings-project-open.png)

Odebrat a znovu načíst projekt. Když projekt znovu načten, spuštění úkolu watch automaticky spuštěn.

## <a name="summary"></a>Souhrn

Grunt je Spouštěč výkonné úloh, která umožňuje automatizovat většinu úloh sestavení klienta. Grunt využívá NPM doručil příslušné balíčky a funkce nástroje integraci se sadou Visual Studio. Visual Studio Task Runner Explorer zjišťuje změny konfiguračních souborů a poskytuje pohodlné rozhraní pro spouštění úloh, zobrazit spuštěné úlohy a vázat úlohy k události aplikace Visual Studio.
