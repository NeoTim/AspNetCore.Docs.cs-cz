<span data-ttu-id="b3daf-101">Přidejte následující vlastnosti pro `Movie` třídy:</span><span class="sxs-lookup"><span data-stu-id="b3daf-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="b3daf-102">`Movie` Třída obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b3daf-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="b3daf-103">`Id` Pole, které vyžaduje databáze pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="b3daf-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="b3daf-104">`[DataType(DataType.Date)]`:  [Datový typ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) atribut určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="b3daf-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="b3daf-105">Pomocí tohoto atributu:</span><span class="sxs-lookup"><span data-stu-id="b3daf-105">With this attribute:</span></span>

  * <span data-ttu-id="b3daf-106">Uživatel není nutné zadávat informace o čase v poli datum.</span><span class="sxs-lookup"><span data-stu-id="b3daf-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="b3daf-107">Pouze datum je zobrazený, není čas informace.</span><span class="sxs-lookup"><span data-stu-id="b3daf-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="b3daf-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou popsané v pozdějších kurzech.</span><span class="sxs-lookup"><span data-stu-id="b3daf-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>