<!-- THIS INCLUDE USED BY MVC AND RP -->
<span data-ttu-id="9f111-101">Do třídy přidejte následující vlastnosti: `Movie`</span><span class="sxs-lookup"><span data-stu-id="9f111-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9f111-102">Třída `Movie` obsahuje:</span><span class="sxs-lookup"><span data-stu-id="9f111-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="9f111-103">Toto `ID` pole je vyžadováno databází pro primární klíč.</span><span class="sxs-lookup"><span data-stu-id="9f111-103">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9f111-104">`[DataType(DataType.Date)]`: Atribut [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) určuje typ dat (datum).</span><span class="sxs-lookup"><span data-stu-id="9f111-104">`[DataType(DataType.Date)]`:  The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (Date).</span></span> <span data-ttu-id="9f111-105">S tímto atributem:</span><span class="sxs-lookup"><span data-stu-id="9f111-105">With this attribute:</span></span>

  * <span data-ttu-id="9f111-106">Uživatel nemusí zadávat informace o čase do pole data.</span><span class="sxs-lookup"><span data-stu-id="9f111-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9f111-107">Zobrazí se pouze datum, nikoli informace o čase.</span><span class="sxs-lookup"><span data-stu-id="9f111-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9f111-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) jsou zahrnuty v pozdější kurz.</span><span class="sxs-lookup"><span data-stu-id="9f111-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>
