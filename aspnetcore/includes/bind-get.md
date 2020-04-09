> [!WARNING]
> Z bezpečnostních důvodů se musíte `GET` přihlásit k vazby dat požadavku na vlastnosti modelu stránky. Před mapováním na vlastnosti ověřte vstup uživatele. Přihlášení do `GET` vazby je užitečné při adresování scénářů, které spoléhají na řetězec dotazu nebo hodnoty postupu.
>
> Chcete-li vytvořit `GET` vazbu vlastnost [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) na `SupportsGet` požadavky, `true`nastavte vlastnost atributu na :
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Další informace naleznete [v tématu ASP.NET Základní komunitní standup: Vazba na diskuzi GET (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
