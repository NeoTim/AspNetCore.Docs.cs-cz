<!-- THIS INCLUDE USED BY MVC AND RP -->
Do `Movie` třídy přidejte následující vlastnosti:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Třída `Movie` obsahuje:

* Pole `ID` vyžaduje databáze pro primární klíč.
* `[DataType(DataType.Date)]`: atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum). S tímto atributem:

  * Uživatel není požádán o zadání informací o čase do pole datum.
  * Zobrazí se pouze datum, nejedná se o informace o čase.

V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .
