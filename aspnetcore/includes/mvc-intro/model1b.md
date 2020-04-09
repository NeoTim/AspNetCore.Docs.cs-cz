Do třídy přidejte následující vlastnosti: `Movie`

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

Třída `Movie` obsahuje:

* Pole, `Id` které je požadováno databází pro primární klíč.
* `[DataType(DataType.Date)]`: Atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat`Date`( ). S tímto atributem:

  * Uživatel nemusí zadávat informace o čase do pole data.
  * Zobrazí se pouze datum, nikoli informace o čase.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou zahrnuty v pozdější kurz.