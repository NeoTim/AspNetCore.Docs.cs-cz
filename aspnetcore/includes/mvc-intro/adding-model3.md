
## <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a klepněte **Mvc Movie** odkaz.
* Klepněte **vytvořit nový** propojit a vytvořit film.

  ![Vytvořit zobrazení s poli pro genre, ceny, datum vydání a název](~/tutorials/first-mvc-app/adding-model/_static/movies.png)

* Není možné zadávat desetinné tečky a čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) pro neanglická národní prostředí, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa. Zobrazit [ https://github.com/aspnet/AspNetCore.Docs/issues/4076 ](https://github.com/aspnet/AspNetCore.Docs/issues/4076) a [další prostředky](#additional-resources) Další informace. Teď zadejte celá čísla, jako je 10.

<a name="displayformatdatelocal"></a>

* V některých národní prostředí je třeba zadat formát data. Viz následující zvýrazněný kód.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateFormat.cs?name=snippet_1&highlight=2,10)]

Budeme mluvit o `DataAnnotations` později v tomto návodu.

Klepnutím na **vytvořit** způsobí, že formulář, který se má publikovat na server, kde je video informace uloženy v databázi. Aplikace přesměruje */Movies* adresu URL, kde se zobrazí informace o nově vytvořený video.

![Výpis film zobrazení zobrazující nově vytvořený filmy](~/tutorials/first-mvc-app/adding-model/_static/h.png)

Vytvořte několik další položky video. Zkuste **upravit**, **podrobnosti**, a **odstranit** odkazy, které jsou všechny funkční.
