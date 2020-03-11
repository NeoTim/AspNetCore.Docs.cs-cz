<span data-ttu-id="0c489-101">Do `Movie` třídy přidejte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0c489-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="0c489-102">Třída `Movie` obsahuje:</span><span class="sxs-lookup"><span data-stu-id="0c489-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="0c489-103">Pole `Id`, které databáze vyžaduje pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="0c489-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="0c489-104">`[DataType(DataType.Date)]`: atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (`Date`).</span><span class="sxs-lookup"><span data-stu-id="0c489-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="0c489-105">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="0c489-105">With this attribute:</span></span>

  * <span data-ttu-id="0c489-106">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="0c489-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="0c489-107">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="0c489-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="0c489-108">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="0c489-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>