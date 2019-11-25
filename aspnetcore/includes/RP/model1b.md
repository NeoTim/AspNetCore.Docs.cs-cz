<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="9cb1f-101">Do `Movie` třídy přidejte následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="9cb1f-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9cb1f-102">Třída `Movie` obsahuje:</span><span class="sxs-lookup"><span data-stu-id="9cb1f-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="9cb1f-103">Pole `ID` vyžaduje databáze pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9cb1f-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9cb1f-104">`[DataType(DataType.Date)]`: atribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) určuje typ dat (datum).</span><span class="sxs-lookup"><span data-stu-id="9cb1f-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="9cb1f-105">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="9cb1f-105">With this attribute:</span></span>

  * <span data-ttu-id="9cb1f-106">Uživatel není požádán o zadání informací o čase do pole datum.</span><span class="sxs-lookup"><span data-stu-id="9cb1f-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9cb1f-107">Zobrazí se pouze datum, nejedná se o informace o čase.</span><span class="sxs-lookup"><span data-stu-id="9cb1f-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9cb1f-108">V pozdějším kurzu jsou uvedena tato [Anotace](/dotnet/api/system.componentmodel.dataannotations) .</span><span class="sxs-lookup"><span data-stu-id="9cb1f-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
