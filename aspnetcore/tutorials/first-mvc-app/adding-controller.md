---
title: Přidání kontroleru aplikace ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak přidat řadič jednoduchou aplikaci ASP.NET Core MVC.
ms.author: riande
ms.date: 02/28/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: f28dc96b66fce736242d26a2584fea0a23375e23
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815196"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a>Přidání kontroleru aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Vzor architektury Model-View-Controller (MVC) rozděluje aplikace do tří hlavních součástí: **M**odelu, **V**tabulky, a **C**ontroller. Vzor MVC pomáhá vytvářet aplikace, které jsou více možností intenzivního testování a aktualizace je snazší než tradiční monolitické aplikace. Aplikace využívající architekturu MVC obsahují:

* **M**odels: Třídy, které představují data aplikace. Tříd modelu pomocí logiku ověřování k vynucení obchodní pravidla pro tato data. Objekty modelu obvykle, načíst a ukládají stav modelu v databázi. V tomto kurzu `Movie` model načte data o filmech z databáze, poskytuje k zobrazení nebo aktualizuje. Aktualizovaná data je zapsána do databáze.

* **V**iews: Zobrazení jsou komponenty, které zobrazují aplikace uživatelského rozhraní (UI). Obecně platí toto uživatelské rozhraní zobrazí data modelu.

* **C**ontrollers: Třídy, které zpracovávají požadavky prohlížeče. Načítají data modelu a volání Zobrazit šablony, které vrátí odpověď. V aplikaci MVC zobrazení pouze zobrazuje informace; kontroler zpracovává a reaguje na vstup uživatele a interakce. Kontroler například zpracovává hodnoty data a řetězce dotazu trasy a předává tyto hodnoty do modelu. Model může být tyto hodnoty použít k dotazování databáze. Například `https://localhost:1234/Home/About` má data trasy z `Home` (controller) a `About` (metoda akce má volat na kontroler home). `https://localhost:1234/Movies/Edit/5` je požadavek na úpravy videa s ID = 5 pomocí film kontroleru. Data trasy, která je vysvětlen později v tomto kurzu.

Vzor MVC pomáhá vytvářet aplikace, které separují jednotlivé aspekty aplikace (logika vstupu, obchodní logika a logika uživatelského rozhraní) současně poskytují volné párování mezi těmito elementy. Vzor Určuje, kde jednotlivé typy logiky se musí nacházet v aplikaci. Logika uživatelského rozhraní patří do zobrazení. Logika vstupu patří do kontroleru. Obchodní logika patří do modelu. Tato separace pomáhá Správa složitých aplikací, když vytvoříte aplikaci, protože umožňuje pracovat na jeden aspekt jejich implementace v čase bez dopadu na jiný kód. Například můžete pracovat na zobrazení kódu bez v závislosti na obchodní logiku kód.

Zahrnují tyto koncepty v této sérii kurzů jsme ukazují, jak se dají použít k vytvoření aplikace movie. Projekt MVC obsahuje složky *řadiče* a *zobrazení*.

## <a name="add-a-controller"></a>Přidání kontroleru

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče > Přidat > kontroler**
  ![kontextové nabídky](adding-controller/_static/add_controller.png)

* V **přidat vygenerované uživatelské rozhraní** dialogu **kontroler MVC – prázdný**

  ![Přidat kontroler MVC s názvem](adding-controller/_static/ac.png)

* V **dialogové okno Přidat prázdný kontroler MVC**, zadejte **HelloWorldController** a vyberte **přidat**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Vyberte **EXPLORER** ikonu a pak kliknutí s klávesou control (klikněte pravým tlačítkem) **řadiče > Nový soubor** a pojmenujte nový soubor *HelloWorldController.cs*.

  ![Kontextové nabídky](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče > Přidat > Nový soubor**.
![Kontextové nabídky](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

Vyberte **ASP.NET Core** a **třída Kontroleru MVC**.

Název kontroleru **HelloWorldController**.

![Přidat kontroler MVC s názvem](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

Nahraďte obsah *Controllers/HelloWorldController.cs* následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

Každý `public` metoda v kontroleru je volatelná jako koncový bod HTTP. V příkladu výše obě metody vrací řetězec. Poznámka: před každou metodu komentáře.

Koncový bod HTTP, jako je targetable adresy URL ve webové aplikaci `https://localhost:5001/HelloWorld`a kombinuje protokol použitý: `HTTPS`, umístění v síti webového serveru (včetně TCP port): `localhost:5001` a cílový identifikátor URI `HelloWorld`.

První komentář stavy jde [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) metody, která je volána přidáním `/HelloWorld/` k základní adrese URL. Určuje druhý komentář [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) metody, která je volána přidáním `/HelloWorld/Welcome/` na adresu URL. V tomto kurzu později modul generování uživatelského rozhraní se používá ke generování `HTTP POST` metody, které aktualizují data.

Spusťte aplikaci v režimu bez ladění a připojit "HelloWorld" na cestu v panelu Adresa. `Index` Metoda vrátí hodnotu typu string.

![Okno prohlížeče zobrazující reakce aplikace na tohoto objektu je Moje výchozí akce](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

MVC volá třídy kontroleru (a metody akce v nich) v závislosti na příchozí adrese URL. Výchozí hodnota [logiku směrování URL](xref:mvc/controllers/routing) používá MVC používá tento formát pro určení kódu, který má být vyvolán:

`/[Controller]/[ActionName]/[Parameters]`

Formátu směrování je nastavena v `Configure` metoda *Startup.cs* souboru.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

Přechod do aplikace a nechcete zadat všechny segmenty adres URL, použije se výchozí kontroler "Home" a zadaná metoda "Index" v šabloně řádek zvýrazněný výše.

První segment adresy URL určuje třída kontroleru pro spuštění. Takže `localhost:xxxx/HelloWorld` mapuje `HelloWorldController` třídy. Druhá část segment adresy URL určí metodu akce v třídě. Takže `localhost:xxxx/HelloWorld/Index` by způsobilo `Index` metodu `HelloWorldController` má třída spustit. Všimněte si, že jste museli procházet `localhost:xxxx/HelloWorld` a `Index` byla volána metoda ve výchozím nastavení. Důvodem je, že `Index` je výchozí metodou, která bude volána na řadiči, pokud nebyl explicitně zadán název metody. Třetí část segment adresy URL ( `id`) je pro data trasy. Data trasy, která je vysvětlen později v tomto kurzu.

Přejděte do `https://localhost:xxxx/HelloWorld/Welcome`. `Welcome` Metoda spustí a vrátí řetězec `This is the Welcome action method...`. Pro tuto adresu URL kontroleru je `HelloWorld` a `Welcome` je metoda akce. Nebyly použity `[Parameters]` část adresy URL ještě.

![Okno prohlížeče zobrazující reakce aplikace na tohoto objektu je metoda úvodní akce](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

Upravte kód předat některé informace o parametrech z adresy URL kontroleru. Například, `/HelloWorld/Welcome?name=Rick&numtimes=4`. Změnit `Welcome` tak, aby zahrnoval dva parametry, jak je znázorněno v následujícím kódu.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

Předchozí kód:

* Volitelný parametr funkce jazyka C# používá k označení, že `numTimes` parametr výchozí hodnotu 1, pokud není pro tento parametr předána žádná hodnota. <!-- remove for simplified -->
* Používá `HtmlEncoder.Default.Encode` k ochraně aplikace před zlými úmysly vstup (konkrétně JavaScript).
* Používá [interpolovaných řetězců](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) v `$"Hello {name}, NumTimes is: {numTimes}"`. <!-- remove for simplified -->

Spusťte aplikaci a přejděte do:

   `https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

(Nahradit xxxx číslem portu.) Můžete vyzkoušet různé hodnoty pro `name` a `numtimes` v adrese URL. MVC [vazby modelu](xref:mvc/models/model-binding) systém automaticky mapují pojmenované parametry z řetězce dotazu do adresního řádku parametrům ve své metodě. Zobrazit [vazby modelu](xref:mvc/models/model-binding) Další informace.

![Okno prohlížeče zobrazující odpověď aplikace Hello Rick, NumTimes je: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

Na obrázku výš, segment adresy URL (`Parameters`) se nepoužívá, `name` a `numTimes` parametry jsou předány jako [řetězce dotazu](https://wikipedia.org/wiki/Query_string). `?` (Otazník) v adrese URL výše je oddělovač a postupujte podle řetězce dotazu. `&` Odděluje řetězce dotazu.

Nahradit `Welcome` metodu s následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

Spusťte aplikaci a zadejte následující adresu URL: `https://localhost:xxx/HelloWorld/Welcome/3?name=Rick`

Tentokrát třetí segment adresy URL odpovídající parametr trasa `id`. `Welcome` Metody obsahuje parametr `id` odpovídající šablony do adresy URL `MapRoute` metoda. Koncové `?` (v `id?`) označuje, `id` parametr je nepovinný.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

V těchto příkladech kontroleru je to "VC" část MVC – to znamená, zobrazení a kontroler fungovat. Kontroler přímo vrací HTML. Obecně nechcete řadiče vrácení HTML přímo, protože, který se stane velmi náročný kód a udržovat. Místo toho budete obvykle používat samostatný soubor šablony zobrazení Razor ke generování odpovědi HTML. Můžete to udělat v dalším kurzu.

> [!div class="step-by-step"]
> [Předchozí](start-mvc.md)
> [další](adding-view.md)
