`<form method="post">` Prvek je [pomocné rutiny značky formuláře](xref:mvc/views/working-with-forms#the-form-tag-helper). Pomocná rutina značky formuláře se automaticky zahrne [antiforgery token](xref:security/anti-request-forgery).

Modul generování uživatelského rozhraní vytvoří kód Razor pro každé pole v modelu (s výjimkou ID) podobný následujícímu:

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

[Pomocných rutin značek ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` a `<span asp-validation-for`) zobrazení chyb ověřování. Ověření se budeme věnovat jednotlivě podrobněji dále v této sérii.

[Pomocné rutiny značky popisek](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generuje titulek popisek a `for` atribut pro `Title` vlastnost.

[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) používá [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta.
