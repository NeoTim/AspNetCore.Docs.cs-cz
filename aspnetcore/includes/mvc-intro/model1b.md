Do `Movie` třídy přidejte následující vlastnosti:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Třída `Movie` obsahuje:

* Pole `Id`, které databáze vyžaduje pro primární klíč.
* `[DataType(DataType.Date)]`: atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (`Date`). S tímto atributem:

  * Uživatel není požádán o zadání informací o čase do pole datum.
  * Zobrazí se pouze datum, nejedná se o informace o čase.

V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .