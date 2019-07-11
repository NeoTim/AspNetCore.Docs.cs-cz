<!--
[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/_Layout.cshtml?highlight=7,31)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull)]

![Index view](~/tutorials/first-mvc-app/search/_static/ghost.png)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

--> 

Předchozí `Index` metody:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_1stSearch)]

Aktualizovaný `Index` metodu s `id` parametr:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,8&name=snippet_SearchID)]

Nyní lze předat název vyhledávání jako data trasy (segment adresy URL) místo jako hodnotu řetězce dotazu.

![Index zobrazení na mapách slovo ghost, přidá do adresy Url a vrácené film seznam dvou filmy, Ghostbusters a Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

Ale nemůžete očekávat, že uživatelům změnit adresu URL pokaždé, když chtějí hledat videa. Takže teď přidáte prvky uživatelského rozhraní, aby to pomohl ostatním filtrovat videa. Pokud jste změnili podpis `Index` metody testování jak předat trasy vázané `ID` parametr, změnit zpět tak, že vyžaduje parametr s názvem `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_1stSearch)]

Otevřít *Views/Movies/Index.cshtml* a přidejte `<form>` značek, jejichž přehled najdete níže:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

Kód HTML `<form>` označení používá [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms), takže při odeslání formuláře řetězec filtru je odeslána do `Index` akce kontroleru videa. Uložte změny a pak test filtru.

![Index zobrazení na mapách slovo ghost, zadaný do textového pole Název filtru](~/tutorials/first-mvc-app/search/_static/filter.png)

Neexistuje žádná `[HttpPost]` přetížení `Index` metody, jako jste očekávali. Není nutné, protože metoda není změněn stav aplikace, stačí filtrování dat.

Můžete přidat následující `[HttpPost] Index` metody.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

`notUsed` Parametr se používá k vytvoření přetížení pro `Index` metody. Budeme mluvit o, který později v tomto kurzu.

Pokud chcete přidat tuto metodu, by odpovídala původce volání akce `[HttpPost] Index` metody a `[HttpPost] Index` spustili byste metodu, jak je znázorněno na následujícím obrázku.

![Okno prohlížeče se odezvy aplikací z indexu HttpPost: filtr na ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

Ale i v případě, že přidáte to `[HttpPost]` verzi `Index` metoda, existuje omezení v tom, jak to vše implementován. Představte si, že chcete konkrétní hledání (záložky) nebo chcete poslat odkaz s přáteli, mohou kliknout, chcete-li zobrazit stejné filtrovaný seznam videa. Všimněte si, že adresa URL pro odeslání požadavku HTTP POST je stejný jako adresu URL pro požadavek na získání (localhost:xxxxx/filmy/Index) – není k dispozici žádné informace o vyhledávání v adrese URL. Vyhledávací řetězec informace jsou odeslány na server jako [tvoří pole hodnota](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Ověřte, že pomocí nástroje pro vývojáře v prohlížeči nebo vynikající [nástroj Fiddler](https://www.telerik.com/fiddler). Následující obrázek ukazuje Developer tools pro prohlížeč Chrome:

![Karta síť vývojářských nástrojů v Microsoft Edge zobrazuje text požadavku s hodnotou hledaný_řetězec ghost](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Parametr hledání můžete zobrazit a [XSRF](xref:security/anti-request-forgery) tokenu v textu požadavku. Všimněte si, jak je uvedeno v předchozím kurzu [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms) generuje [XSRF](xref:security/anti-request-forgery) token proti padělání. Jsme nejsou úpravu dat, proto nepotřebujeme ověřit token v metodě kontroleru.

Protože parametr hledání v textu požadavku a nikoliv adresu URL, nelze zachytit těchto hledání informací na záložku nebo sdílet s ostatními. Opravíme to tak, že zadáte, by měl být požadavek `HTTP GET`.
