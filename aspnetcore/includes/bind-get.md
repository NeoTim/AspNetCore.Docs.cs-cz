> [!WARNING]
> Z bezpečnostních důvodů je potřeba, abyste se přihlásíte k vazbě `GET` dat žádostí na vlastnosti modelu stránky. Před mapováním na vlastnosti ověřte vstup uživatele. Přizpůsobování `GET` vazby je užitečné při adresování scénářů, které spoléhají na řetězec dotazu nebo hodnoty tras.
>
> Chcete-li vytvořit navázání vlastnosti u požadavků na `GET`, nastavte vlastnost `SupportsGet` atributu [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) na `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Další informace najdete v tématu [ASP.NET Core Community rychlou schůzku: Bind on GET diskuze (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
