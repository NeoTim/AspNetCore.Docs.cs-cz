---
title: Přidání kontroleru do aplikace ASP.NET Core MVC
author: rick-anderson
description: Naučte se, jak přidat kontroler do jednoduché aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: 57b85708f4cded99b8f008a69830cb104cd8e879
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914238"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Přidání kontroleru do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Architektonický vzor architektury MVC (Model-View-Controller) odděluje aplikaci na tři hlavní komponenty: **M**Odel, **V a v** **C**ontroller. Vzor MVC vám pomůže vytvářet aplikace, které jsou více testovatelné a snáze aktualizovat než tradiční aplikace monolitické. Aplikace založené na MVC obsahují:

* **M**Odels: třídy, které reprezentují data aplikace. Třídy modelu používají ověřovací logiku k vyhodnocování obchodních pravidel pro tato data. Objekty modelu obvykle načítají a ukládají stav modelu v databázi. V tomto kurzu model `Movie` načítá z databáze filmové data a poskytuje ji zobrazení nebo aktualizuje. Aktualizovaná data jsou zapsána do databáze.

* **V**Iews: zobrazení jsou komponenty, které zobrazují uživatelské rozhraní (UI) aplikace. Obecně toto uživatelské rozhraní zobrazuje data modelu.

* **C**Ontrollers: třídy, které zpracovávají požadavky prohlížeče. Načítají data modelu a šablony zobrazení volání, které vracejí odpověď. V aplikaci MVC zobrazení zobrazuje pouze informace; kontroler zpracovává a reaguje na vstupy a interakce uživatele. Například kontroler zpracovává směrování dat a hodnot řetězce dotazu a předá tyto hodnoty do modelu. Model může tyto hodnoty použít k dotazování databáze. Například `https://localhost:5001/Home/Privacy` má směrovat data `Home` (kontroler) a `Privacy` (metoda Action pro volání na domovském řadiči). `https://localhost:5001/Movies/Edit/5` je požadavek na úpravu filmu s ID = 5 pomocí kontroleru filmů. Údaje o trasách jsou vysvětleny dále v tomto kurzu.

Vzor MVC vám pomůže vytvářet aplikace, které oddělují různé aspekty aplikace (vstupní logika, obchodní logika a logika uživatelského rozhraní), a současně poskytuje volné spojení mezi těmito prvky. Vzor určuje, kde by měl být každý druh logiky umístěný v aplikaci. Logika uživatelského rozhraní patří do zobrazení. Logika vstupu patří do kontroleru. Obchodní logika patří do modelu. Toto oddělení vám pomůže se správou složitosti při sestavování aplikace, protože umožňuje pracovat na jednom aspektu implementace v čase, aniž by to mělo vliv na jiný kód. Můžete například pracovat s kódem zobrazení bez závislosti na kódu obchodní logiky.

Tyto koncepty popisujeme v této sérii kurzů a ukážeme vám, jak je použít k vytvoření filmové aplikace. Projekt MVC obsahuje složky pro *řadiče* a *zobrazení*.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče > přidat >** 
  ![kontextové nabídce](adding-controller/_static/add_controller.png)

* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **kontroler MVC – prázdné** .

  ![Přidat kontroler MVC a pojmenovat](adding-controller/_static/ac.png)

* V **dialogovém okně Přidat prázdný řadič MVC**zadejte **HelloWorldController** a vyberte **Přidat**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vyberte ikonu **Průzkumníka** a potom klikněte na řídicí panel (klikněte pravým tlačítkem myši) **> nový soubor** a pojmenujte nový soubor *HelloWorldController.cs*.

  ![Kontextová nabídka](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče > přidat > nový soubor**.
Kontextová nabídka ![](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Vyberte **ASP.NET Core** a **třídu kontroleru MVC**.

Pojmenujte kontroler **HelloWorldController**.

![Přidat kontroler MVC a pojmenovat](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Obsah *Controllers/HelloWorldController. cs* nahraďte následujícím způsobem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Každá metoda `public` v kontroleru se může volat jako koncový bod HTTP. V ukázce výše obě metody vrací řetězec. Všimněte si komentářů před jednotlivými metodami.

Koncový bod HTTP je cílová adresa URL ve webové aplikaci, například `https://localhost:5001/HelloWorld`, a kombinuje použitý protokol: `HTTPS`, umístění v síti webového serveru (včetně portu TCP): `localhost:5001` a cílový identifikátor URI `HelloWorld`.

První komentář uvádí metodu [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) , která je vyvolána připojením `/HelloWorld/` k základní adrese URL. Druhý komentář určuje metodu [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) , která je vyvolána připojením `/HelloWorld/Welcome/` k adrese URL. Později v tomto kurzu se modul generování uživatelského rozhraní používá ke generování `HTTP POST` metod, které aktualizují data.

Spusťte aplikaci v režimu bez ladění a přidejte "HelloWorld" do cesty na adresním řádku. Metoda `Index` vrací řetězec.

![Okno prohlížeče zobrazující odezvu aplikace na toto je moje výchozí akce.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC vyvolá třídy kontroleru (a metody akcí v nich) v závislosti na příchozí adrese URL. Výchozí [logika směrování adres URL](xref:mvc/controllers/routing) , kterou používá MVC, používá formát podobný tomuto: k určení kódu, který se má vyvolat:

`/[Controller]/[ActionName]/[Parameters]`

Formát směrování je nastaven v metodě `Configure` v souboru *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

Když přejdete do aplikace a nezadáte žádné segmenty adresy URL, použije se výchozí řídicí řadič a metoda "index" zadaná na řádku šablony, který se výše zvýrazní.

První segment adresy URL určuje třídu kontroleru, která se má spustit. Proto `localhost:{PORT}/HelloWorld` namapována na třídu kontroleru **HelloWorld**. Druhá část segmentu adresy URL určuje metodu Action třídy. Takže `localhost:{PORT}/HelloWorld/Index` by mohla být spuštěna metoda `Index` třídy `HelloWorldController`. Všimněte si, že jste museli procházet pouze `localhost:{PORT}/HelloWorld` a ve výchozím nastavení byla volána metoda `Index`. To je proto, že `Index` je výchozí metoda, která bude volána na řadiči, pokud není explicitně zadán název metody. Třetí část segmentu adresy URL (`id`) je určena pro data směrování. Údaje o trasách jsou vysvětleny dále v tomto kurzu.

Přejděte do `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` se spustí a vrátí řetězec `This is the Welcome action method...`. Pro tuto adresu URL se kontroler `HelloWorld` a `Welcome` je metoda Action. Zatím jste nepoužili `[Parameters]` část této adresy URL.

![Okno prohlížeče ukazující odezvu aplikace na toto je metoda akce Welcome](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Upravte kód tak, aby předával některé informace o parametrech z adresy URL kontroleru. Například `/HelloWorld/Welcome?name=Rick&numtimes=4`. Změňte metodu `Welcome` tak, aby zahrnovala dva parametry, jak je znázorněno v následujícím kódu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Předchozí kód:

* Používá funkci C# volitelného parametru k označení, že parametr `numTimes` má výchozí hodnotu 1, pokud není pro tento parametr předána žádná hodnota. <!-- remove for simplified -->
* Používá `HtmlEncoder.Default.Encode` k ochraně aplikace před škodlivým vstupem (konkrétně JavaScript).
* Používá [interpolované řetězce](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) v `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Spusťte aplikaci a přejděte do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Nahraďte `{PORT}` číslem portu.) Můžete zkusit jiné hodnoty pro `name` a `numtimes` v adrese URL. Systém [vazby modelu](xref:mvc/models/model-binding) MVC automaticky mapuje pojmenované parametry z řetězce dotazu v adresním řádku na parametry v metodě. Další informace najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .

![Okno prohlížeče zobrazující odezvu aplikace Hello Rick, NumTimes je\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na obrázku výše se nepoužívá segment adresy URL (`Parameters`), parametry `name` a `numTimes` jsou předány v [řetězci dotazu](https://wikipedia.org/wiki/Query_string). `?` (otazník) na výše uvedené adrese URL je oddělovač a následuje řetězec dotazu. Znak `&` odděluje páry pole a hodnoty.

Nahraďte metodu `Welcome` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Spusťte aplikaci a zadejte následující adresu URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tentokrát se třetí segment adresy URL shodoval s parametrem trasy `id`. Metoda `Welcome` obsahuje parametr `id`, který se shodoval s šablonou URL v metodě `MapControllerRoute`. Koncová `?` (v `id?`) značí, že parametr `id` je volitelný.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

V těchto příkladech kontroler provedl "VC" část MVC – to znamená, **že v obrazovém panelu a**v **C**ontroller Work. Kontroler přímo vrací HTML. Obecně nechcete, aby řadiče vracely kód HTML přímo, protože to může být velmi náročné na kód a údržbu. Místo toho k vygenerování odpovědi HTML obvykle používáte samostatný soubor šablony zobrazení Razor. Provedete to v dalším kurzu.

> [!div class="step-by-step"]
> [Předchozí](start-mvc.md)
> [Další](adding-view.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Architektonický vzor architektury MVC (Model-View-Controller) odděluje aplikaci na tři hlavní komponenty: **M**Odel, **V a v** **C**ontroller. Vzor MVC vám pomůže vytvářet aplikace, které jsou více testovatelné a snáze aktualizovat než tradiční aplikace monolitické. Aplikace založené na MVC obsahují:

* **M**Odels: třídy, které reprezentují data aplikace. Třídy modelu používají ověřovací logiku k vyhodnocování obchodních pravidel pro tato data. Objekty modelu obvykle načítají a ukládají stav modelu v databázi. V tomto kurzu model `Movie` načítá z databáze filmové data a poskytuje ji zobrazení nebo aktualizuje. Aktualizovaná data jsou zapsána do databáze.

* **V**Iews: zobrazení jsou komponenty, které zobrazují uživatelské rozhraní (UI) aplikace. Obecně toto uživatelské rozhraní zobrazuje data modelu.

* **C**Ontrollers: třídy, které zpracovávají požadavky prohlížeče. Načítají data modelu a šablony zobrazení volání, které vracejí odpověď. V aplikaci MVC zobrazení zobrazuje pouze informace; kontroler zpracovává a reaguje na vstupy a interakce uživatele. Například kontroler zpracovává směrování dat a hodnot řetězce dotazu a předá tyto hodnoty do modelu. Model může tyto hodnoty použít k dotazování databáze. Například `https://localhost:5001/Home/About` má směrovat data `Home` (kontroler) a `About` (metoda Action pro volání na domovském řadiči). `https://localhost:5001/Movies/Edit/5` je požadavek na úpravu filmu s ID = 5 pomocí kontroleru filmů. Údaje o trasách jsou vysvětleny dále v tomto kurzu.

Vzor MVC vám pomůže vytvářet aplikace, které oddělují různé aspekty aplikace (vstupní logika, obchodní logika a logika uživatelského rozhraní), a současně poskytuje volné spojení mezi těmito prvky. Vzor určuje, kde by měl být každý druh logiky umístěný v aplikaci. Logika uživatelského rozhraní patří do zobrazení. Logika vstupu patří do kontroleru. Obchodní logika patří do modelu. Toto oddělení vám pomůže se správou složitosti při sestavování aplikace, protože umožňuje pracovat na jednom aspektu implementace v čase, aniž by to mělo vliv na jiný kód. Můžete například pracovat s kódem zobrazení bez závislosti na kódu obchodní logiky.

Tyto koncepty popisujeme v této sérii kurzů a ukážeme vám, jak je použít k vytvoření filmové aplikace. Projekt MVC obsahuje složky pro *řadiče* a *zobrazení*.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče > přidat >** 
  ![kontextové nabídce](adding-controller/_static/add_controller.png)

* V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **kontroler MVC – prázdné** .

  ![Přidat kontroler MVC a pojmenovat](adding-controller/_static/ac.png)

* V **dialogovém okně Přidat prázdný řadič MVC**zadejte **HelloWorldController** a vyberte **Přidat**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vyberte ikonu **Průzkumníka** a potom klikněte na řídicí panel (klikněte pravým tlačítkem myši) **> nový soubor** a pojmenujte nový soubor *HelloWorldController.cs*.

  ![Kontextová nabídka](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V **Průzkumník řešení**klikněte pravým tlačítkem na **řadiče > přidat > nový soubor**.
Kontextová nabídka ![](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Vyberte **ASP.NET Core** a **třídu kontroleru MVC**.

Pojmenujte kontroler **HelloWorldController**.

![Přidat kontroler MVC a pojmenovat](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Obsah *Controllers/HelloWorldController. cs* nahraďte následujícím způsobem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Každá metoda `public` v kontroleru se může volat jako koncový bod HTTP. V ukázce výše obě metody vrací řetězec. Všimněte si komentářů před jednotlivými metodami.

Koncový bod HTTP je cílová adresa URL ve webové aplikaci, například `https://localhost:5001/HelloWorld`, a kombinuje použitý protokol: `HTTPS`, umístění v síti webového serveru (včetně portu TCP): `localhost:5001` a cílový identifikátor URI `HelloWorld`.

První komentář uvádí metodu [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) , která je vyvolána připojením `/HelloWorld/` k základní adrese URL. Druhý komentář určuje metodu [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) , která je vyvolána připojením `/HelloWorld/Welcome/` k adrese URL. Později v tomto kurzu se modul generování uživatelského rozhraní používá ke generování `HTTP POST` metod, které aktualizují data.

Spusťte aplikaci v režimu bez ladění a přidejte "HelloWorld" do cesty na adresním řádku. Metoda `Index` vrací řetězec.

![Okno prohlížeče zobrazující odezvu aplikace na toto je moje výchozí akce.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC vyvolá třídy kontroleru (a metody akcí v nich) v závislosti na příchozí adrese URL. Výchozí [logika směrování adres URL](xref:mvc/controllers/routing) , kterou používá MVC, používá formát podobný tomuto: k určení kódu, který se má vyvolat:

`/[Controller]/[ActionName]/[Parameters]`

Formát směrování je nastaven v metodě `Configure` v souboru *Startup.cs* .

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Když přejdete do aplikace a nezadáte žádné segmenty adresy URL, použije se výchozí řídicí řadič a metoda "index" zadaná na řádku šablony, který se výše zvýrazní.

První segment adresy URL určuje třídu kontroleru, která se má spustit. Proto `localhost:{PORT}/HelloWorld` namapuje na třídu `HelloWorldController`. Druhá část segmentu adresy URL určuje metodu Action třídy. Takže `localhost:{PORT}/HelloWorld/Index` by mohla být spuštěna metoda `Index` třídy `HelloWorldController`. Všimněte si, že jste museli procházet pouze `localhost:{PORT}/HelloWorld` a ve výchozím nastavení byla volána metoda `Index`. Důvodem je, že `Index` je výchozí metoda, která bude volána na řadiči, pokud název metody není explicitně zadán. Třetí část segmentu adresy URL (`id`) je určena pro data směrování. Údaje o trasách jsou vysvětleny dále v tomto kurzu.

Přejděte do `https://localhost:{PORT}/HelloWorld/Welcome`. Metoda `Welcome` se spustí a vrátí řetězec `This is the Welcome action method...`. Pro tuto adresu URL se kontroler `HelloWorld` a `Welcome` je metoda Action. Zatím jste nepoužili `[Parameters]` část této adresy URL.

![Okno prohlížeče ukazující odezvu aplikace na toto je metoda akce Welcome](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Upravte kód tak, aby předával některé informace o parametrech z adresy URL kontroleru. Například `/HelloWorld/Welcome?name=Rick&numtimes=4`. Změňte metodu `Welcome` tak, aby zahrnovala dva parametry, jak je znázorněno v následujícím kódu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Předchozí kód:

* Používá funkci C# volitelného parametru k označení, že parametr `numTimes` má výchozí hodnotu 1, pokud není pro tento parametr předána žádná hodnota. <!-- remove for simplified -->
* Používá `HtmlEncoder.Default.Encode` k ochraně aplikace před škodlivým vstupem (konkrétně JavaScript).
* Používá [interpolované řetězce](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) v `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Spusťte aplikaci a přejděte do:

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

(Nahraďte `{PORT}` číslem portu.) Můžete zkusit jiné hodnoty pro `name` a `numtimes` v adrese URL. Systém [vazby modelu](xref:mvc/models/model-binding) MVC automaticky mapuje pojmenované parametry z řetězce dotazu v adresním řádku na parametry v metodě. Další informace najdete v tématu [vazba modelu](xref:mvc/models/model-binding) .

![Okno prohlížeče zobrazující odezvu aplikace Hello Rick, NumTimes je\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na obrázku výše se nepoužívá segment adresy URL (`Parameters`), parametry `name` a `numTimes` jsou předány v [řetězci dotazu](https://wikipedia.org/wiki/Query_string). `?` (otazník) na výše uvedené adrese URL je oddělovač a následuje řetězec dotazu. Znak `&` odděluje páry pole a hodnoty.

Nahraďte metodu `Welcome` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Spusťte aplikaci a zadejte následující adresu URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`

Tentokrát se třetí segment adresy URL shodoval s parametrem trasy `id`. Metoda `Welcome` obsahuje parametr `id`, který se shodoval s šablonou URL v metodě `MapRoute`. Koncová `?` (v `id?`) značí, že parametr `id` je volitelný.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

V těchto příkladech kontroler provedl "VC" část MVC – to znamená, že zobrazení a kontroler funguje. Kontroler přímo vrací HTML. Obecně nechcete, aby řadiče vracely kód HTML přímo, protože to může být velmi náročné na kód a údržbu. Místo toho se obvykle používá samostatný soubor šablony zobrazení Razor, který vám umožní vygenerovat odpověď HTML. Provedete to v dalším kurzu.

> [!div class="step-by-step"]
> [Předchozí](start-mvc.md)
> [Další](adding-view.md)

::: moniker-end
