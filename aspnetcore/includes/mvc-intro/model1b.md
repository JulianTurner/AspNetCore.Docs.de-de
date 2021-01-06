<span data-ttu-id="c5250-101">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="c5250-101">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="c5250-102">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="c5250-102">The `Movie` class contains:</span></span>

* <span data-ttu-id="c5250-103">Das `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c5250-103">The `Id` field which is required by the database for the primary key.</span></span>
* <span data-ttu-id="c5250-104">`[DataType(DataType.Date)]`:  Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="c5250-104">`[DataType(DataType.Date)]`:  The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="c5250-105">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="c5250-105">With this attribute:</span></span>

  * <span data-ttu-id="c5250-106">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="c5250-106">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="c5250-107">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="c5250-107">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="c5250-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="c5250-108">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>