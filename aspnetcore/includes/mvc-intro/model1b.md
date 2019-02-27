Přidejte následující vlastnosti pro `Movie` třídy:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

`Movie` Třída obsahuje:

* `Id` Pole, které vyžaduje databáze pro primární klíč.
* `[DataType(DataType.Date)]`:  [Datový typ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) atribut určuje typ dat (`Date`). Pomocí tohoto atributu:

  * Uživatel není nutné zadávat informace o čase v poli datum.
  * Pouze datum je zobrazený, není čas informace.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou popsané v pozdějších kurzech.