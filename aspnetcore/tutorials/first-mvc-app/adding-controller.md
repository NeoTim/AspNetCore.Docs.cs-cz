---
title: Přidání ovladače do aplikace Core MVC ASP.NET
author: rick-anderson
description: Přečtěte si, jak přidat ovladač do jednoduché aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666990"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Přidání ovladače do aplikace Core MVC ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Model-View-Controller (MVC) architektonický vzor odděluje aplikaci do tří hlavních součástí: **M**odel, **V**tj. a **C**ontroller. Vzor MVC vám pomůže vytvářet aplikace, které jsou více testovatelné a snadněji aktualizovatelné než tradiční monolitické aplikace. Aplikace založené na MVC obsahují:

* **M**odels: Třídy, které představují data aplikace. Třídy modelu používají logiku ověření k vynucení obchodních pravidel pro tato data. Objekty modelu obvykle načítají a ukládají stav modelu v databázi. V tomto kurzu `Movie` model načte filmová data z databáze, poskytuje je do zobrazení nebo jej aktualizuje. Aktualizovaná data jsou zapsána do databáze.

* **V**iews: Zobrazení jsou součásti, které zobrazují uživatelské rozhraní aplikace (UI). Obecně platí, že toto uI zobrazí data modelu.

* **C**ontrollers: Třídy, které zpracovávají požadavky prohlížeče. Načítají data modelu a šablony zobrazení volání, které vracejí odpověď. V aplikaci MVC zobrazení zobrazuje pouze informace; ovladač zpracovává a reaguje na vstup a interakci uživatele. Například řadič zpracovává hodnoty směrování dat a řetězce dotazu a předá tyto hodnoty modelu. Model může použít tyto hodnoty k dotazování databáze. Například `https://localhost:5001/Home/Privacy` má data `Home` trasy (kontroleru) a `Privacy` (metoda akce pro volání na domácím řadiči). `https://localhost:5001/Movies/Edit/5`je požadavek na úpravu filmu pomocí ID=5 pomocí filmového ovladače. Data trasy jsou vysvětlena dále v kurzu.

Vzor MVC pomáhá vytvářet aplikace, které oddělují různé aspekty aplikace (vstupní logika, obchodní logika a logika uživatelského rozhraní) a zároveň poskytují volné párování mezi těmito prvky. Vzor určuje, kde by měl být každý druh logiky umístěn v aplikaci. Logika ui patří do zobrazení. Vstupní logika patří do řadiče. Obchodní logika patří do modelu. Toto oddělení pomáhá spravovat složitost při vytváření aplikace, protože umožňuje pracovat na jednom aspektu implementace najednou bez dopadu na kód jiného. Můžete například pracovat na kódu zobrazení bez v závislosti na kódu obchodní logiky.

Tyto koncepty pokrýváme v této sérii kurzů a ukážeme vám, jak je používat k vytvoření filmové aplikace. Projekt MVC obsahuje složky pro *řadiče* a *zobrazení*.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řadiče > kontextová nabídka Přidat > řadiče.**
  ![](adding-controller/_static/add_controller.png)

* V dialogovém okně **Přidat písmo** vyberte **Ovladač MVC – prázdný.**

  ![Přidejte řadič MVC a pojmenujte jej](adding-controller/_static/ac.png)

* V **dialogovém okně Přidat prázdný řadič MVC**zadejte **HelloWorldController** a vyberte **PŘIDAT**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vyberte ikonu **EXPLORER** a potom klepněte na ovladače (po kliknutí pravým tlačítkem myši) **> nový soubor** a pojmenujte nový soubor *HelloWorldController.cs*.

  ![Kontextová nabídka](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řadiče > přidat > nový soubor**.
![Kontextová nabídka](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Vyberte **ASP.NET třídu řadiče jádra** a **MVC**.

Pojmenujte řadič **HelloWorldController**.

![Přidejte řadič MVC a pojmenujte jej](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Nahraďte obsah *controllers/HelloWorldController.cs* následujícím:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Každá `public` metoda v řadiči je volatelná jako koncový bod HTTP. Ve výše uvedeném vzorku obě metody vrátí řetězec. Všimněte si komentáře předcházející jednotlivé metody.

Koncový bod HTTP je cílitelná adresa URL `https://localhost:5001/HelloWorld`ve webové aplikaci, `HTTPS`například , a kombinuje použitý protokol: , `localhost:5001` síťové umístění `HelloWorld`webového serveru (včetně portu TCP): a cílový identifikátor URI .

První komentář uvádí, že se jedná o metodu `/HelloWorld/` [HTTP GET,](https://www.w3schools.com/tags/ref_httpmethods.asp) která je vyvolána připojením k základní adrese URL. Druhý komentář určuje metodu [HTTP GET,](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) která je `/HelloWorld/Welcome/` vyvolána připojením k adrese URL. Později v tutoriálu generování uživatelského stroji `HTTP POST` se používá ke generování metod, které aktualizují data.

Spusťte aplikaci v režimu bez ladění a připojte "HelloWorld" k cestě v adresním řádku. Metoda `Index` vrátí řetězec.

![Okno prohlížeče zobrazující odpověď aplikace Toto je moje výchozí akce](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC vyvolá třídy kontroleru (a metody akce v nich) v závislosti na příchozí adrese URL. Výchozí [logika směrování adres URL](xref:mvc/controllers/routing) používaná mvc používá formát, jako je tento k určení, jaký kód vyvolat:

`/[Controller]/[ActionName]/[Parameters]`

Formát směrování je nastaven `Configure` v metodě v *Startup.cs* souboru.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Když přejdete do aplikace a nezadáte žádné segmenty URL, výchozí nastavení "Home" řadič a "Index" metoda zadaná v řádku šablony zvýrazněné výše.

První segment adresy URL určuje třídu řadiče ke spuštění. Takže `localhost:{PORT}/HelloWorld` mapy **helloworld**controller třídy. Druhá část segmentu URL určuje metodu akce ve třídě. Tak `localhost:{PORT}/HelloWorld/Index` by `Index` způsobit spuštění `HelloWorldController` metody třídy. Všimněte si, že `localhost:{PORT}/HelloWorld` jste `Index` museli pouze procházet a metoda byla volána ve výchozím nastavení. Je to `Index` proto, že je výchozí metoda, která bude volána na řadiči, pokud název metody není explicitně zadán. Třetí část segmentu URL `id`( ) je pro data trasy. Data trasy jsou vysvětlena dále v kurzu.

Přejděte na `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` spustí a vrátí `This is the Welcome action method...`řetězec . Pro tuto adresu URL `HelloWorld` `Welcome` je a je metoda akce. Část adresy URL `[Parameters]` jste ještě nepoužili.

![Okno prohlížeče zobrazující odpověď aplikace Toto je metoda uvítací akce](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Upravte kód tak, aby předává některé informace o parametrech z adresy URL do řadiče. Například, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Změňte `Welcome` metodu tak, aby zahrnovala dva parametry, jak je znázorněno v následujícím kódu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Předcházející kód:

* Používá c# volitelné parametr funkce k `numTimes` označení, že parametr výchozí 1, pokud žádná hodnota je předána pro tento parametr. <!-- remove for simplified -->
* Používá `HtmlEncoder.Default.Encode` se k ochraně aplikace před škodlivým vstupem (jmenovitě JavaScript).
* Používá [interpolované řetězce](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) `$"Hello {name}, NumTimes is: {numTimes}"`v . <!-- remove for simplified -->

Spusťte aplikaci a přejděte na:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Nahraďte `{PORT}` číslem portu.) Můžete vyzkoušet různé `name` `numtimes` hodnoty pro a v adrese URL. Systém [vazby modelu](xref:mvc/models/model-binding) MVC automaticky mapuje pojmenované parametry z řetězce dotazu v adresním řádku na parametry ve vaší metodě. Další informace naleznete [v tématu Vazba modelu.](xref:mvc/models/model-binding)

![Okno prohlížeče zobrazující odpověď aplikace Hello Rick, NumTimes je\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na obrázku výše se`Parameters`segment URL ( ) `name` `numTimes` nepoužívá, parametry a jsou předány v [řetězci dotazu](https://wikipedia.org/wiki/Query_string). `?` (otazník) ve výše uvedené adrese URL je oddělovač a následuje řetězec dotazu. Znak `&` odděluje dvojice polí a hodnot.

Nahraďte metodu `Welcome` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Spusťte aplikaci a zadejte následující adresu URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tentokrát třetí segment URL odpovídal parametru `id`trasy . Metoda `Welcome` obsahuje parametr, `id` který odpovídá šabloně `MapControllerRoute` adresy URL v metodě. Koncové `?` (in `id?`) označuje, že `id` parametr je volitelný.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

V těchto příkladech regulátor dělá "VC" část MVC - to znamená, **že V**tj a **C**ontroller práce. Řadič vrací HTML přímo. Obecně nechcete, aby řadiče vracet HTML přímo, protože to se stává velmi těžkopádné kód a udržovat. Místo toho obvykle používáte samostatný soubor šablony zobrazení Razor ke generování odpovědi HTML. Můžete to udělat v dalším tutoriálu.

> [!div class="step-by-step"]
> [Předchozí](start-mvc.md)
> [další](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Model-View-Controller (MVC) architektonický vzor odděluje aplikaci do tří hlavních součástí: **M**odel, **V**tj. a **C**ontroller. Vzor MVC vám pomůže vytvářet aplikace, které jsou více testovatelné a snadněji aktualizovatelné než tradiční monolitické aplikace. Aplikace založené na MVC obsahují:

* **M**odels: Třídy, které představují data aplikace. Třídy modelu používají logiku ověření k vynucení obchodních pravidel pro tato data. Objekty modelu obvykle načítají a ukládají stav modelu v databázi. V tomto kurzu `Movie` model načte filmová data z databáze, poskytuje je do zobrazení nebo jej aktualizuje. Aktualizovaná data jsou zapsána do databáze.

* **V**iews: Zobrazení jsou součásti, které zobrazují uživatelské rozhraní aplikace (UI). Obecně platí, že toto uI zobrazí data modelu.

* **C**ontrollers: Třídy, které zpracovávají požadavky prohlížeče. Načítají data modelu a šablony zobrazení volání, které vracejí odpověď. V aplikaci MVC zobrazení zobrazuje pouze informace; ovladač zpracovává a reaguje na vstup a interakci uživatele. Například řadič zpracovává hodnoty směrování dat a řetězce dotazu a předá tyto hodnoty modelu. Model může použít tyto hodnoty k dotazování databáze. Například `https://localhost:5001/Home/About` má data `Home` trasy (kontroleru) a `About` (metoda akce pro volání na domácím řadiči). `https://localhost:5001/Movies/Edit/5`je požadavek na úpravu filmu pomocí ID=5 pomocí filmového ovladače. Data trasy jsou vysvětlena dále v kurzu.

Vzor MVC pomáhá vytvářet aplikace, které oddělují různé aspekty aplikace (vstupní logika, obchodní logika a logika uživatelského rozhraní) a zároveň poskytují volné párování mezi těmito prvky. Vzor určuje, kde by měl být každý druh logiky umístěn v aplikaci. Logika ui patří do zobrazení. Vstupní logika patří do řadiče. Obchodní logika patří do modelu. Toto oddělení pomáhá spravovat složitost při vytváření aplikace, protože umožňuje pracovat na jednom aspektu implementace najednou bez dopadu na kód jiného. Můžete například pracovat na kódu zobrazení bez v závislosti na kódu obchodní logiky.

Tyto koncepty pokrýváme v této sérii kurzů a ukážeme vám, jak je používat k vytvoření filmové aplikace. Projekt MVC obsahuje složky pro *řadiče* a *zobrazení*.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řadiče > kontextová nabídka Přidat > řadiče.**
  ![](adding-controller/_static/add_controller.png)

* V dialogovém okně **Přidat písmo** vyberte **Ovladač MVC – prázdný.**

  ![Přidejte řadič MVC a pojmenujte jej](adding-controller/_static/ac.png)

* V **dialogovém okně Přidat prázdný řadič MVC**zadejte **HelloWorldController** a vyberte **PŘIDAT**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vyberte ikonu **EXPLORER** a potom klepněte na ovladače (po kliknutí pravým tlačítkem myši) **> nový soubor** a pojmenujte nový soubor *HelloWorldController.cs*.

  ![Kontextová nabídka](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **položku Řadiče > přidat > nový soubor**.
![Kontextová nabídka](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Vyberte **ASP.NET třídu řadiče jádra** a **MVC**.

Pojmenujte řadič **HelloWorldController**.

![Přidejte řadič MVC a pojmenujte jej](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Nahraďte obsah *controllers/HelloWorldController.cs* následujícím:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Každá `public` metoda v řadiči je volatelná jako koncový bod HTTP. Ve výše uvedeném vzorku obě metody vrátí řetězec. Všimněte si komentáře předcházející jednotlivé metody.

Koncový bod HTTP je cílitelná adresa URL `https://localhost:5001/HelloWorld`ve webové aplikaci, `HTTPS`například , a kombinuje použitý protokol: , `localhost:5001` síťové umístění `HelloWorld`webového serveru (včetně portu TCP): a cílový identifikátor URI .

První komentář uvádí, že se jedná o metodu `/HelloWorld/` [HTTP GET,](https://www.w3schools.com/tags/ref_httpmethods.asp) která je vyvolána připojením k základní adrese URL. Druhý komentář určuje metodu [HTTP GET,](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) která je `/HelloWorld/Welcome/` vyvolána připojením k adrese URL. Později v tutoriálu generování uživatelského stroji `HTTP POST` se používá ke generování metod, které aktualizují data.

Spusťte aplikaci v režimu bez ladění a připojte "HelloWorld" k cestě v adresním řádku. Metoda `Index` vrátí řetězec.

![Okno prohlížeče zobrazující odpověď aplikace Toto je moje výchozí akce](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC vyvolá třídy kontroleru (a metody akce v nich) v závislosti na příchozí adrese URL. Výchozí [logika směrování adres URL](xref:mvc/controllers/routing) používaná mvc používá formát, jako je tento k určení, jaký kód vyvolat:

`/[Controller]/[ActionName]/[Parameters]`

Formát směrování je nastaven `Configure` v metodě v *Startup.cs* souboru.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Když přejdete do aplikace a nezadáte žádné segmenty URL, výchozí nastavení "Home" řadič a "Index" metoda zadaná v řádku šablony zvýrazněné výše.

První segment adresy URL určuje třídu řadiče ke spuštění. Takže `localhost:{PORT}/HelloWorld` mapy `HelloWorldController` do třídy. Druhá část segmentu URL určuje metodu akce ve třídě. Tak `localhost:{PORT}/HelloWorld/Index` by `Index` způsobit spuštění `HelloWorldController` metody třídy. Všimněte si, že `localhost:{PORT}/HelloWorld` jste `Index` museli pouze procházet a metoda byla volána ve výchozím nastavení. Důvodem `Index` je výchozí metoda, která bude volána na řadiči, pokud název metody není explicitně zadán. Třetí část segmentu URL `id`( ) je pro data trasy. Data trasy jsou vysvětlena dále v kurzu.

Přejděte na `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` spustí a vrátí `This is the Welcome action method...`řetězec . Pro tuto adresu URL `HelloWorld` `Welcome` je a je metoda akce. Část adresy URL `[Parameters]` jste ještě nepoužili.

![Okno prohlížeče zobrazující odpověď aplikace Toto je metoda uvítací akce](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Upravte kód tak, aby předává některé informace o parametrech z adresy URL do řadiče. Například, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Změňte `Welcome` metodu tak, aby zahrnovala dva parametry, jak je znázorněno v následujícím kódu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Předcházející kód:

* Používá c# volitelné parametr funkce k `numTimes` označení, že parametr výchozí 1, pokud žádná hodnota je předána pro tento parametr. <!-- remove for simplified -->
* Používá `HtmlEncoder.Default.Encode` se k ochraně aplikace před škodlivým vstupem (jmenovitě JavaScript).
* Používá [interpolované řetězce](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) `$"Hello {name}, NumTimes is: {numTimes}"`v . <!-- remove for simplified -->

Spusťte aplikaci a přejděte na:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Nahraďte `{PORT}` číslem portu.) Můžete vyzkoušet různé `name` `numtimes` hodnoty pro a v adrese URL. Systém [vazby modelu](xref:mvc/models/model-binding) MVC automaticky mapuje pojmenované parametry z řetězce dotazu v adresním řádku na parametry ve vaší metodě. Další informace naleznete [v tématu Vazba modelu.](xref:mvc/models/model-binding)

![Okno prohlížeče zobrazující odpověď aplikace Hello Rick, NumTimes je\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na obrázku výše se`Parameters`segment URL ( ) `name` `numTimes` nepoužívá, parametry a jsou předány v [řetězci dotazu](https://wikipedia.org/wiki/Query_string). `?` (otazník) ve výše uvedené adrese URL je oddělovač a následuje řetězec dotazu. Znak `&` odděluje dvojice polí a hodnot.

Nahraďte metodu `Welcome` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Spusťte aplikaci a zadejte následující adresu URL:`https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tentokrát třetí segment URL odpovídal parametru `id`trasy . Metoda `Welcome` obsahuje parametr, `id` který odpovídá šabloně `MapRoute` adresy URL v metodě. Koncové `?` (in `id?`) označuje, že `id` parametr je volitelný.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

V těchto příkladech řadič provádí část "VC" MVC - to znamená, že zobrazení a kontrolor práce. Řadič vrací HTML přímo. Obecně nechcete, aby řadiče vracet HTML přímo, protože to se stává velmi těžkopádné kód a udržovat. Místo toho obvykle používáte samostatný soubor šablony zobrazení Razor, který pomáhá generovat odpověď HTML. Můžete to udělat v dalším tutoriálu.

> [!div class="step-by-step"]
> [Předchozí](start-mvc.md)
> [další](adding-view.md)

::: moniker-end
