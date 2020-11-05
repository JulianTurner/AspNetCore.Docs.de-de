---
title: 'Teil 5: :::no-loc(Razor)::: Pages mit EF Core in ASP.NET Core (Datenmodell)'
author: rick-anderson
description: 'Dies ist Teil 5 der Tutorialreihe zu :::no-loc(Razor)::: Pages und dem Entity Framework.'
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 1ac9d6303daac82f3973c5d027fe1f453dc32e02
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054098"
---
# <a name="part-5-no-locrazor-pages-with-ef-core-in-aspnet-core---data-model"></a><span data-ttu-id="5f3d0-103">Teil 5: :::no-loc(Razor)::: Pages mit EF Core in ASP.NET Core (Datenmodell)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-103">Part 5, :::no-loc(Razor)::: Pages with EF Core in ASP.NET Core - Data Model</span></span>

<span data-ttu-id="5f3d0-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f3d0-105">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="5f3d0-106">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-106">In this tutorial:</span></span>

* <span data-ttu-id="5f3d0-107">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="5f3d0-108">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="5f3d0-109">Das vollständige Datenmodell wird in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-109">The completed data model is shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="5f3d0-111">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-111">The Student entity</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="5f3d0-113">Ersetzen Sie den Code in *Models/Student.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="5f3d0-114">Der Code oben fügt eine `FullName`-Eigenschaft hinzu und fügt den vorhandenen Eigenschaften die folgenden Attribute hinzu:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="5f3d0-115">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="5f3d0-115">The FullName calculated property</span></span>

<span data-ttu-id="5f3d0-116">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="5f3d0-117">`FullName` kann nicht festgelegt werden und verfügt daher lediglich über einen get-Accessor.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="5f3d0-118">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="5f3d0-119">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="5f3d0-120">Für die Anmeldedaten von Studenten zeigen alle Webseiten derzeit die Zeit und das Datum an, obwohl nur das Datum relevant ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="5f3d0-121">Indem Sie Attribute für die Datenanmerkung verwenden, können Sie eine Codeänderungen vornehmen, durch die das Anzeigeformat auf jeder Seite korrigiert wird, in der die Daten angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="5f3d0-122">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5f3d0-123">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="5f3d0-124">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="5f3d0-125">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-125">For example:</span></span>

* <span data-ttu-id="5f3d0-126">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="5f3d0-127">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="5f3d0-128">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="5f3d0-129">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="5f3d0-130">Das DisplayFormat-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="5f3d0-131">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5f3d0-132">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="5f3d0-133">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="5f3d0-134">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="5f3d0-135">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="5f3d0-136">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="5f3d0-137">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="5f3d0-138">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="5f3d0-139">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="5f3d0-140">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="5f3d0-141">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="5f3d0-142">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links und einigen clientseitigen Eingabevalidierungen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="5f3d0-143">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="5f3d0-144">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="5f3d0-145">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="5f3d0-146">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="5f3d0-147">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="5f3d0-148">Der gezeigte Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="5f3d0-149">Ein Beispiel, das die minimale Zeichenfolgenlänge festlegt, wird [später](#the-required-attribute) gezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="5f3d0-150">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="5f3d0-151">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="5f3d0-152">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="5f3d0-153">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) kann verwendet werden, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="5f3d0-154">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3d0-156">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="5f3d0-158">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="5f3d0-159">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="5f3d0-160">Wenn eine Migration später in diesem Tutorial erstellt und angewendet wird, werden die Namensfelder `nvarchar(50)` als Ergebnis der Attribute für die Zeichenfolgenlänge.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f3d0-162">Überprüfen Sie in Ihrem SQLite-Tool die Spaltendefinitionen für die `Student`-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="5f3d0-163">Die Namensfelder weisen den Typ `Text` auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-163">The name fields have type `Text`.</span></span> <span data-ttu-id="5f3d0-164">Beachten Sie, dass das Feld für den Vornamen den Namen `FirstMidName` trägt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="5f3d0-165">Im nächsten Abschnitt ändern Sie den Namen dieser Spalte in `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="5f3d0-166">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="5f3d0-167">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="5f3d0-168">Im `Student`-Modell wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="5f3d0-169">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="5f3d0-170">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="5f3d0-171">Mit dem `[Column]`-Attribut wird `Student.FirstMidName` im Datenmodell der Spalte `FirstName` in der Tabelle `Student` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="5f3d0-172">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="5f3d0-173">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="5f3d0-174">Diese Diskrepanz wird durch Hinzufügen einer Migration zu einem späteren Zeitpunkt in diesem Tutorial aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="5f3d0-175">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="5f3d0-176">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="5f3d0-177">Das `Required`-Attribut ist für Typen, die keine NULL-Werte annehmen können (etwas Werttypen, z.B. `DateTime`, `int` und `double`) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="5f3d0-178">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="5f3d0-179">Das `Required`-Attribut muss mit `MinimumLength` verwendet werden, damit die `MinimumLength` erzwungen wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="5f3d0-180">`MinimumLength` und `Required` lassen Leerzeichen zu, um die Überprüfung zu bestehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="5f3d0-181">Verwenden Sie das `RegularExpression`-Attribut, um vollständige Kontrolle über die Zeichenfolge zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="5f3d0-182">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="5f3d0-183">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="5f3d0-184">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="5f3d0-185">Erstellen einer Migration</span><span class="sxs-lookup"><span data-stu-id="5f3d0-185">Create a migration</span></span>

<span data-ttu-id="5f3d0-186">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="5f3d0-187">Es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-187">An exception is thrown.</span></span> <span data-ttu-id="5f3d0-188">Das `[Column]`-Attribut bewirkt, dass EF eine Spalte mit dem Namen `FirstName` erwartet, aber der Spaltenname in der Datenbank ist weiterhin `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3d0-190">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="5f3d0-191">Geben Sie in der PMC folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="5f3d0-192">Mit dem ersten dieser Befehle wird die folgende Warnmeldung generiert:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="5f3d0-193">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="5f3d0-194">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="5f3d0-195">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-195">Open the Student table in SSOX:</span></span>

  ![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="5f3d0-197">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="5f3d0-198">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="5f3d0-199">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f3d0-201">Die Fehlermeldung lautet ungefähr wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="5f3d0-202">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-202">Open a command window in the project folder.</span></span> <span data-ttu-id="5f3d0-203">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="5f3d0-204">Der Befehl für die Datenbankaktualisierung zeigt einen Fehler wie im folgenden Beispiel an:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="5f3d0-205">In diesem Tutorial wird die anfängliche Migration gelöscht und erneut erstellt, um diese Fehlermeldung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="5f3d0-206">Weitere Informationen finden Sie im SQLite-Warnhinweis am Anfang des [Migrationstutorials](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="5f3d0-207">Löschen Sie den Ordner *Migrations*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="5f3d0-208">Führen Sie die folgenden Befehle aus, um die Datenbank zu löschen, eine neue anfängliche Migration zu erstellen und die Migration anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="5f3d0-209">Überprüfen Sie die Tabelle „Student“ in Ihrem SQLite-Tool.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="5f3d0-210">Die Spalte, die zunächst FirstMidName hieß, ist jetzt FirstName.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="5f3d0-211">Führen Sie die Anwendung aus. Wechseln Sie zur Studentenseite.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="5f3d0-212">Beachten Sie, dass die Uhrzeiten nicht mit Datumsangaben eingegeben oder angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="5f3d0-213">Klicken Sie auf **Neu erstellen** , und versuchen Sie, einen Namen einzugeben, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-213">Select **Create New** , and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="5f3d0-214">In den folgenden Abschnitten werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="5f3d0-215">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="5f3d0-216">Die Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="5f3d0-216">The Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="5f3d0-218">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="5f3d0-219">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="5f3d0-220">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="5f3d0-221">Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-221">Navigation properties</span></span>

<span data-ttu-id="5f3d0-222">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="5f3d0-223">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5f3d0-224">Ein Dozent kann höchstens ein Büro haben, sodass die `OfficeAssignment`-Eigenschaft eine einzelne `OfficeAssignment`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="5f3d0-225">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="5f3d0-226">Die Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-226">The OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="5f3d0-228">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="5f3d0-229">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-229">The Key attribute</span></span>

<span data-ttu-id="5f3d0-230">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="5f3d0-231">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="5f3d0-232">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="5f3d0-233">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="5f3d0-234">EF Core kann `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment` erkennen, da `InstructorID` nicht den ID- oder classnameID-Benennungskonventionen folgt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="5f3d0-235">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="5f3d0-236">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="5f3d0-237">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-237">The Instructor navigation property</span></span>

<span data-ttu-id="5f3d0-238">Die `Instructor.OfficeAssignment`-Navigationseigenschaft kann NULL sein, da möglicherweise keine `OfficeAssignment`-Zeile für einen bestimmten Dozenten vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="5f3d0-239">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="5f3d0-240">Die `OfficeAssignment.Instructor`-Navigationseigenschaft hat immer eine Instructor-Entität, da der Typ `InstructorID` des Fremdschlüssels `int` ist, ein Typ, der keine NULL-Werte annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="5f3d0-241">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="5f3d0-242">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="5f3d0-243">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-243">The Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="5f3d0-245">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="5f3d0-246">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="5f3d0-247">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="5f3d0-248">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="5f3d0-249">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="5f3d0-250">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="5f3d0-251">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="5f3d0-252">Durch explizites Einschließen des Fremdschlüssels in das Datenmodell können Updates jedoch einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="5f3d0-253">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID`*nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="5f3d0-254">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="5f3d0-255">Die `Department`-Eigenschaft ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="5f3d0-256">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="5f3d0-257">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="5f3d0-258">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="5f3d0-259">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="5f3d0-260">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="5f3d0-261">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="5f3d0-262">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="5f3d0-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="5f3d0-263">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="5f3d0-264">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="5f3d0-265">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="5f3d0-266">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-267">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-268">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="5f3d0-269">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="5f3d0-270">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="5f3d0-271">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5f3d0-272">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="5f3d0-273">Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-273">The Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="5f3d0-275">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="5f3d0-276">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-276">The Column attribute</span></span>

<span data-ttu-id="5f3d0-277">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="5f3d0-278">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="5f3d0-279">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="5f3d0-280">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-280">Column mapping is generally not required.</span></span> <span data-ttu-id="5f3d0-281">Entity Framework Core wählt den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="5f3d0-282">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="5f3d0-283">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-284">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-285">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="5f3d0-286">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="5f3d0-287">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-287">An administrator is always an instructor.</span></span> <span data-ttu-id="5f3d0-288">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="5f3d0-289">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="5f3d0-290">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="5f3d0-291">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="5f3d0-292">Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="5f3d0-293">Das Standardverhalten kann zu Zirkelregeln für kaskadierende Löschvorgänge führen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="5f3d0-294">Durch Zirkelregeln für kaskadierende Löschvorgänge wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="5f3d0-295">Wenn die `Department.InstructorID`-Eigenschaft beispielsweise als nicht auf NULL festlegbare Eigenschaft definiert wurde, konfiguriert EF Core eine Löschregel für kaskadierende Löschvorgänge.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="5f3d0-296">In diesem Fall würde der Fachbereich gelöscht, wenn der als Administrator zugewiesene Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="5f3d0-297">In diesem Szenario wäre eine Einschränkungsregel sinnvoller.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="5f3d0-298">Die folgende [Fluent-API](#fluent-api-alternative-to-attributes) würde eine Einschränkungsregel festlegen und das kaskadierende Delete deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="5f3d0-299">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-299">The Enrollment entity</span></span>

<span data-ttu-id="5f3d0-300">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-300">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="5f3d0-302">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-303">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-304">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5f3d0-305">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="5f3d0-306">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="5f3d0-307">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="5f3d0-308">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="5f3d0-309">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="5f3d0-310">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="5f3d0-311">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="5f3d0-312">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="5f3d0-313">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-313">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="5f3d0-315">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="5f3d0-316">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="5f3d0-317">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="5f3d0-318">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="5f3d0-319">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="5f3d0-320">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="5f3d0-321">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-321">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="5f3d0-323">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="5f3d0-324">Die m:n-Beziehung zwischen „Instructor“ und „Courses“ erfordert eine Jointabelle, und die Entität für diese Jointabelle ist „CourseAssignment“.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5f3d0-326">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="5f3d0-327">Der Name der Jointabelle für „Instructor“ und „Courses“ würde mithilfe dieses Musters beispielsweise `CourseInstructor` lauten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="5f3d0-328">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="5f3d0-329">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-329">Data models start out simple and grow.</span></span> <span data-ttu-id="5f3d0-330">Jointabellen ohne Nutzlast (PJTs) entwickeln sich häufig so, dass sie eine Nutzlast beinhalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="5f3d0-331">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="5f3d0-332">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="5f3d0-333">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="5f3d0-334">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment``CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="5f3d0-335">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5f3d0-335">Composite key</span></span>

<span data-ttu-id="5f3d0-336">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="5f3d0-337">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="5f3d0-338">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="5f3d0-339">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="5f3d0-340">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="5f3d0-341">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-341">The composite key ensures that:</span></span>

* <span data-ttu-id="5f3d0-342">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="5f3d0-343">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="5f3d0-344">Mehrere Zeilen für denselben Dozenten und Kurs unzulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="5f3d0-345">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="5f3d0-346">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="5f3d0-347">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="5f3d0-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="5f3d0-348">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="5f3d0-349">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="5f3d0-350">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="5f3d0-350">Update the database context</span></span>

<span data-ttu-id="5f3d0-351">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="5f3d0-352">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="5f3d0-353">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="5f3d0-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="5f3d0-354">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="5f3d0-355">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="5f3d0-356">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="5f3d0-357">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="5f3d0-358">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="5f3d0-359">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="5f3d0-360">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="5f3d0-361">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="5f3d0-362">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="5f3d0-363">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="5f3d0-364">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="5f3d0-365">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="5f3d0-366">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="5f3d0-367">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="5f3d0-368">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="5f3d0-369">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="5f3d0-370">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="5f3d0-371">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="5f3d0-372">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="5f3d0-373">Entitätsdiagramm</span><span class="sxs-lookup"><span data-stu-id="5f3d0-373">Entity diagram</span></span>

<span data-ttu-id="5f3d0-374">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="5f3d0-376">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="5f3d0-377">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="5f3d0-378">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="5f3d0-379">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="5f3d0-380">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-380">Seed the database</span></span>

<span data-ttu-id="5f3d0-381">Aktualisieren Sie den Code in *Data/DbInitializer.cs* :</span><span class="sxs-lookup"><span data-stu-id="5f3d0-381">Update the code in *Data/DbInitializer.cs* :</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="5f3d0-382">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="5f3d0-383">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="5f3d0-384">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-384">The sample data is used for testing.</span></span> <span data-ttu-id="5f3d0-385">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="5f3d0-386">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="5f3d0-386">Add a migration</span></span>

<span data-ttu-id="5f3d0-387">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-387">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3d0-389">Führen Sie in der PMC den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="5f3d0-390">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="5f3d0-391">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="5f3d0-392">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler zu behandeln ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f3d0-394">Wenn Sie eine Migration hinzufügen und den `database update`-Befehl ausführen, wird der folgende Fehler generiert:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="5f3d0-395">Im nächsten Abschnitt erfahren Sie, wie dieser Fehler vermieden werden kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="5f3d0-396">Anwenden der Migration oder Löschen und erneutes Erstellen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="5f3d0-397">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="5f3d0-398">In diesem Tutorial werden zwei Alternativen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="5f3d0-399">[Löschen und Neuerstellen der Datenbank](#drop).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="5f3d0-400">Wählen Sie diesen Abschnitt aus, wenn Sie SQLite verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="5f3d0-401">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="5f3d0-402">Die Anleitungen in diesem Abschnitt funktionieren nur für SQL Server, **nicht für SQLite**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="5f3d0-403">Beide Optionen funktionieren für SQL Server.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="5f3d0-404">Obwohl die apply-migration-Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5f3d0-405">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-405">Drop and re-create the database</span></span>

<span data-ttu-id="5f3d0-406">[Überspringen Sie diesen Abschnitt](#apply-the-migration), wenn Sie SQL Server verwenden und den apply-migration-Ansatz im folgenden Abschnitt ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="5f3d0-407">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f3d0-409">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="5f3d0-410">Löschen Sie den Ordner *Migrations* , und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5f3d0-412">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5f3d0-413">Der Projektordner enthält die Datei *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="5f3d0-414">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="5f3d0-415">Löschen Sie den Ordner *Migrations* , und führen Sie dann den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="5f3d0-416">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-416">Run the app.</span></span> <span data-ttu-id="5f3d0-417">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5f3d0-418">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3d0-420">Öffnen Sie die Datenbank in SSOX:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="5f3d0-421">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="5f3d0-422">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-422">Expand the **Tables** node.</span></span> <span data-ttu-id="5f3d0-423">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-423">The created tables are displayed.</span></span>

  ![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="5f3d0-425">Überprüfen Sie die Tabelle **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="5f3d0-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="5f3d0-426">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment** , und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="5f3d0-427">Überprüfen Sie, ob die Tabelle **CourseAssignment** -Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f3d0-430">Verwenden Sie das SQLite-Tool, um die Datenbank zu untersuchen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="5f3d0-431">Neue Tabellen und Spalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-431">New tables and columns.</span></span>
* <span data-ttu-id="5f3d0-432">Mit Daten aufgefüllte Tabellen, z.B. die **CourseAssignment** -Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="5f3d0-433">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="5f3d0-433">Apply the migration</span></span>

<span data-ttu-id="5f3d0-434">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-434">This section is optional.</span></span> <span data-ttu-id="5f3d0-435">Diese Schritte funktionieren nur für SQL Server LocalDB und nur dann, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5f3d0-436">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="5f3d0-437">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="5f3d0-438">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="5f3d0-439">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="5f3d0-440">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) abgeschlossen haben.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5f3d0-441">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="5f3d0-442">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="5f3d0-443">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="5f3d0-444">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="5f3d0-445">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="5f3d0-446">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="5f3d0-447">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="5f3d0-448">Aktualisieren Sie in der `ComplexDataModel`-Migrationsklasse die `Up`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="5f3d0-449">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="5f3d0-450">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="5f3d0-451">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-451">Add the following highlighted code.</span></span> <span data-ttu-id="5f3d0-452">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="5f3d0-453">Durch die zuvor durchgeführten Änderungen sind die vorhandenen `Course`-Zeilen mit dem Fachbereich „Temp“ verknüpft, nachdem die Methode `ComplexDataModel.Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="5f3d0-454">Die Vorgehensweise zum Behandeln der hier gezeigten Situation wurde für dieses Tutorial vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="5f3d0-455">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-455">A production app would:</span></span>

* <span data-ttu-id="5f3d0-456">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="5f3d0-457">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="5f3d0-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5f3d0-459">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="5f3d0-460">Da die `DbInitializer.Initialize`-Methode nur für die Verwendung mit einer leeren Datenbank konzipiert ist, löschen Sie mithilfe von SSOX alle Zeilen in den Tabellen „Student“ und „Course“.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="5f3d0-461">(Der kaskadierende Löschvorgang übernimmt die Tabelle „Enrollment“.)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5f3d0-463">Wenn Sie SQL Server LocalDB mit Visual Studio Code verwenden, führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="5f3d0-464">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-464">Run the app.</span></span> <span data-ttu-id="5f3d0-465">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5f3d0-466">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5f3d0-467">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5f3d0-467">Next steps</span></span>

<span data-ttu-id="5f3d0-468">In den nächsten beiden Tutorials wird gezeigt, wie verwandte Daten gelesen und aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5f3d0-469">[Vorheriges Tutorial](xref:data/ef-rp/migrations)
> [Nächstes Tutorial](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f3d0-470">In den vorherigen Tutorials wurde mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="5f3d0-471">In diesem Tutorial wird Folgendes durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-471">In this tutorial:</span></span>

* <span data-ttu-id="5f3d0-472">Weitere Entitäten und Beziehungen werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="5f3d0-473">Das Datenmodell wird angepasst, indem Regeln zur Formatierung, Validierung und Datenbankzuordnung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="5f3d0-474">Die Entitätsklassen des vollständigen Datenmodells werden in der folgenden Abbildung dargestellt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="5f3d0-476">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-476">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="5f3d0-477">Anpassen des Datenmodells mithilfe von Attributen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-477">Customize the data model with attributes</span></span>

<span data-ttu-id="5f3d0-478">In diesem Abschnitt wird das Datenmodell mithilfe von Attributen angepasst.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="5f3d0-479">Das DataType-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-479">The DataType attribute</span></span>

<span data-ttu-id="5f3d0-480">Die Seite für Studenten zeigt derzeit die Uhrzeit des Anmeldedatums an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="5f3d0-481">Üblicherweise zeigen Datumsfelder nur das Datum an, nicht die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="5f3d0-482">Aktualisieren Sie *Models/Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="5f3d0-483">Das [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute)-Attribut gibt einen Datentyp an, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="5f3d0-484">In diesem Fall sollte nur das Datum angezeigt werden, nicht das Datum und die Uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="5f3d0-485">Die [DataType](/dotnet/api/system.componentmodel.dataannotations.datatype)-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“. Das `DataType`-Attribut kann der App auch das Bereitstellen typspezifischer Features ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="5f3d0-486">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-486">For example:</span></span>

* <span data-ttu-id="5f3d0-487">Der Link `mailto:` wird automatisch für `DataType.EmailAddress` erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="5f3d0-488">Die Datumsauswahl für `DataType.Date` wird in den meisten Browsern bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="5f3d0-489">Das `DataType`-Attribut gibt `data-`-HTML5-Attribute (ausgesprochen „Datadash“) aus, die von HTML5-Browsern genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="5f3d0-490">Die `DataType`-Attribute bieten keine Validierung.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="5f3d0-491">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="5f3d0-492">Standardmäßig wird das Datumsfeld gemäß den Standardformaten basierend auf der [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support)-Klasse des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="5f3d0-493">Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="5f3d0-494">Die `ApplyFormatInEditMode`-Einstellung gibt an, dass die Formatierung ebenfalls auf die Benutzeroberfläche für die Bearbeitung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="5f3d0-495">Einige Felder sollten `ApplyFormatInEditMode` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="5f3d0-496">Das Währungssymbol sollte beispielsweise nicht im Textfeld für die Bearbeitung angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="5f3d0-497">Das `DisplayFormat`-Attribut kann eigenständig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="5f3d0-498">Meist empfiehlt es sich, das `DataType`-Attribut mit dem `DisplayFormat`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="5f3d0-499">Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="5f3d0-500">Das `DataType`-Attribut bietet folgende Vorteile, die in `DisplayFormat` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="5f3d0-501">Der Browser kann HTML5-Features aktivieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="5f3d0-502">Dazu zählen beispielsweise das Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="5f3d0-503">Standardmäßig rendert der Browser Daten mit dem auf Ihrem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="5f3d0-504">Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="5f3d0-505">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-505">Run the app.</span></span> <span data-ttu-id="5f3d0-506">Navigieren Sie zur Indexseite „Studenten“.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="5f3d0-507">Die Uhrzeiten werden nicht mehr angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-507">Times are no longer displayed.</span></span> <span data-ttu-id="5f3d0-508">Jede Ansicht, die das `Student`-Modell verwendet, zeigt das Datum ohne Uhrzeit an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="5f3d0-510">Das StringLength-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-510">The StringLength attribute</span></span>

<span data-ttu-id="5f3d0-511">Die Regeln für die Datenvalidierung und Meldungen für Validierungsfehler können mithilfe von Attributen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="5f3d0-512">Das [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute)-Attribut gibt den mindestens erforderlichen und maximal zulässigen Wert für die Zeichenlänge eines Datenfelds an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="5f3d0-513">Das `StringLength`-Attribut stellt ebenfalls die clientseitige und serverseitige Validierung bereit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="5f3d0-514">Der mindestens erforderliche Wert hat keine Auswirkungen auf das Datenbankschema.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="5f3d0-515">Aktualisieren Sie das `Student`-Modell mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="5f3d0-516">Der vorangehende Code beschränkt Namen auf maximal 50 Zeichen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="5f3d0-517">Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="5f3d0-518">Das Attribut [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) wird verwendet, um Einschränkungen auf die Eingabe anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="5f3d0-519">Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

<span data-ttu-id="5f3d0-520">Führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-520">Run the app:</span></span>

* <span data-ttu-id="5f3d0-521">Navigieren Sie zur Seite für Studenten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="5f3d0-522">Klicken Sie auf **Neu erstellen** , und geben Sie einen Namen ein, der länger als 50 Zeichen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-522">Select **Create New** , and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="5f3d0-523">Wenn Sie auf **Erstellen** klicken, zeigt die clientseitige Validierung eine Fehlermeldung an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-523">Select **Create** , client-side validation shows an error message.</span></span>

![Indexseite „Studenten“, die Fehler für die Zeichenfolgenlänge anzeigt](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="5f3d0-525">Öffnen Sie im **SQL Server-Objekt-Explorer** (SSOX) den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabelle „Students“ im SSOX vor der Migration](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="5f3d0-527">In der vorangehenden Abbildung wird das Schema der `Student`-Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="5f3d0-528">Die Namensfelder weisen den Typ `nvarchar(MAX)` auf, da die Migrationen nicht auf der Datenbank ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="5f3d0-529">Wenn die Migrationen im Verlauf dieses Tutorials ausgeführt werden, ändern sich die Namensfelder in `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="5f3d0-530">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-530">The Column attribute</span></span>

<span data-ttu-id="5f3d0-531">Durch Attribute kann gesteuert werden, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="5f3d0-532">In diesem Abschnitt wird das `Column`-Attribut verwendet, um den Namen der `FirstMidName`-Eigenschaft in der Datenbank auf „FirstName“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="5f3d0-533">Wenn die Datenbank erstellt wird, werden die Eigenschaftsnamen des Moduls für Spaltennamen verwendet (außer bei Verwendung des `Column`-Attributs).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="5f3d0-534">Das `Student`-Modell verwendet `FirstMidName` für das Feld „first-name“, da dieses ebenfalls einen Zweitnamen enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="5f3d0-535">Aktualisieren Sie die Datei *Student.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="5f3d0-536">Durch die zuvor vorgenommene Änderung wird `Student.FirstMidName` in der App der `FirstName`-Spalte der `Student`-Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="5f3d0-537">Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="5f3d0-538">Das Modell, das `SchoolContext` unterstützt, stimmt nicht mehr mit der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="5f3d0-539">Wenn die App ausgeführt wird, bevor Migrationen angewendet werden, wird folgende Ausnahme generiert:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="5f3d0-540">So aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-540">To update the DB:</span></span>

* <span data-ttu-id="5f3d0-541">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-541">Build the project.</span></span>
* <span data-ttu-id="5f3d0-542">Öffnen Sie ein Befehlsfenster im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-542">Open a command window in the project folder.</span></span> <span data-ttu-id="5f3d0-543">Geben Sie folgende Befehle ein, um eine neue Migration zu erstellen und die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="5f3d0-546">Der Befehl `migrations add ColumnFirstName` generiert folgende Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="5f3d0-547">Die Warnung wird generiert, weil die Namensfelder nun auf 50 Zeichen beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="5f3d0-548">Wenn der Name der Datenbank aus mehr als 50 Zeichen besteht, gehen alle Zeichen ab dem 51. verloren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="5f3d0-549">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-549">Test the app.</span></span>

<span data-ttu-id="5f3d0-550">Öffnen Sie die Tabelle „Student“ im SSOX:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-550">Open the Student table in SSOX:</span></span>

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="5f3d0-552">Bevor die Migration angewendet wurde, wiesen die Namensspalten den Typ [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="5f3d0-553">Die Namensspalten weisen nun den Typ `nvarchar(50)` auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="5f3d0-554">Der Spaltenname wurde von `FirstMidName` in `FirstName` geändert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="5f3d0-555">Im folgenden Abschnitt werden beim Erstellen der App in einigen Phasen Compilerfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="5f3d0-556">Diese Anweisungen präzisieren, wann die App erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="5f3d0-557">Aktualisieren der Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-557">Student entity update</span></span>

![Entität „Student“](complex-data-model/_static/student-entity.png)

<span data-ttu-id="5f3d0-559">Aktualisieren Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="5f3d0-560">Das Attribut „Required“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-560">The Required attribute</span></span>

<span data-ttu-id="5f3d0-561">Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="5f3d0-562">Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (`DateTime`, `int`, `double` usw.) nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="5f3d0-563">Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="5f3d0-564">Das `Required`-Attribut kann durch einen Parameter mit der mindestens erforderlichen Länge im `StringLength`-Attribut ersetzt werden:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="5f3d0-565">Das Display-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-565">The Display attribute</span></span>

<span data-ttu-id="5f3d0-566">Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="5f3d0-567">Bei der Standardbeschriftung wurden die Wörter nicht durch einen Leerraum geteilt (z.B. „LastName“).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="5f3d0-568">Die berechnete FullName-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="5f3d0-568">The FullName calculated property</span></span>

<span data-ttu-id="5f3d0-569">Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="5f3d0-570">`FullName` verfügt lediglich über einen get-Accessor und kann daher nicht festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="5f3d0-571">In der Datenbank wird keine `FullName`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="5f3d0-572">Erstellen der Instructor-Entität</span><span class="sxs-lookup"><span data-stu-id="5f3d0-572">Create the Instructor Entity</span></span>

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="5f3d0-574">Erstellen Sie *Models/Instructor.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="5f3d0-575">In einer Zeile können mehrere Attribute enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="5f3d0-576">Das `HireDate`-Attribut kann folgendermaßen geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="5f3d0-577">Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="5f3d0-578">Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="5f3d0-579">Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5f3d0-580">Folgendes gilt, wenn eine Navigationseigenschaft mehrere Entitäten enthält:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="5f3d0-581">Bei der Eigenschaft muss es sich um einen Listentyp handeln, bei dem Einträge hinzugefügt, gelöscht und aktualisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="5f3d0-582">Folgendes gilt für die Typen von Navigationseigenschaften:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="5f3d0-583">Wenn `ICollection<T>` angegeben wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="5f3d0-584">Die `CourseAssignment`-Entität wird im Abschnitt über m:n-Beziehungen erläutert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="5f3d0-585">Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="5f3d0-586">Die `OfficeAssignment`-Eigenschaft enthält also eine einzige `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="5f3d0-587">`OfficeAssignment` ist NULL, wenn kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="5f3d0-588">Erstellen der Entität „OfficeAssignment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-588">Create the OfficeAssignment entity</span></span>

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="5f3d0-590">Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="5f3d0-591">Das Key-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-591">The Key attribute</span></span>

<span data-ttu-id="5f3d0-592">Das `[Key]`-Attribut wird verwendet, um eine Eigenschaft als Primärschlüssel zu identifizieren, wenn der Eigenschaftsname nicht „classnameID“ oder „ID“ entspricht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="5f3d0-593">Es besteht eine 1:0..1-Beziehung zwischen der `Instructor`- und der `OfficeAssignment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="5f3d0-594">Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="5f3d0-595">Der Primärschlüssel `OfficeAssignment` ist ebenfalls der Fremdschlüssel der `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="5f3d0-596">Entity Framework Core erkennt `InstructorID` nicht automatisch als Primärschlüssel von `OfficeAssignment`, weil</span><span class="sxs-lookup"><span data-stu-id="5f3d0-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="5f3d0-597">`InstructorID` der Namenskonvention „ID“ oder „classnameID“ nicht folgt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="5f3d0-598">Deshalb wird das `Key`-Attribut verwendet, um `InstructorID` als Primärschlüssel zu erkennen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="5f3d0-599">Standardmäßig behandelt Entity Framework Core den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="5f3d0-600">Die Navigationseigenschaft „Instructor“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-600">The Instructor navigation property</span></span>

<span data-ttu-id="5f3d0-601">Die `OfficeAssignment`-Navigationseigenschaft für die `Instructor`-Entität ist auf NULL festlegbar, weil:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="5f3d0-602">Referenztypen (z.B. Klassen) auf NULL festlegbar sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="5f3d0-603">Einem Dozenten möglicherweise kein Büro zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="5f3d0-604">Die `OfficeAssignment`-Entität besitzt eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft, weil:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="5f3d0-605">`InstructorID` nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="5f3d0-606">Eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="5f3d0-607">Wenn die Entität `Instructor` mit der Entität `OfficeAssignment` verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="5f3d0-608">Das `[Required]`-Attribut kann auf die `Instructor`-Navigationseigenschaft angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="5f3d0-609">Der vorangehende Code legt fest, dass ein zugehöriger Dozent vorhanden sein muss.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="5f3d0-610">Der vorangehende Code ist nicht erforderlich, da der Fremdschlüssel `InstructorID` (der ebenfalls der Primärschlüssel ist) nicht auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="5f3d0-611">Ändern der Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-611">Modify the Course Entity</span></span>

![Entität „Course“](complex-data-model/_static/course-entity.png)

<span data-ttu-id="5f3d0-613">Aktualisieren Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="5f3d0-614">Die `Course`-Entität besitzt die Fremdschlüsseleigenschaft `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="5f3d0-615">`DepartmentID` zeigt auf die verknüpfte `Department`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="5f3d0-616">Die `Course`-Entität besitzt eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="5f3d0-617">Entity Framework Core erfordert keine Fremdschlüsseleigenschaft für ein Datenmodell, wenn das Modell über eine Navigationseigenschaft für eine verknüpfte Entität verfügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="5f3d0-618">Entity Framework Core erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="5f3d0-619">Entity Framework Core erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für automatisch erstellte Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="5f3d0-620">Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="5f3d0-621">Betrachten Sie beispielsweise ein Modell, bei dem die Fremdschlüsseleigenschaft `DepartmentID`*nicht* enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="5f3d0-622">Folgendes wird durchgeführt, wenn eine Course-Entität zum Bearbeiten abgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="5f3d0-623">Die `Department`-Entität ist NULL, wenn diese nicht explizit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="5f3d0-624">Die `Department`-Entität muss abgerufen werden, um die Course-Entität zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="5f3d0-625">Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell enthalten ist, muss die `Department`-Entität vor einem Update nicht abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="5f3d0-626">Das Attribut „DatabaseGenerated“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="5f3d0-627">Das `[DatabaseGenerated(DatabaseGeneratedOption.None)]`-Attribut gibt an, dass der Primärschlüssel von der Anwendung bereitgestellt wird, statt von der Datenbank generiert zu werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="5f3d0-628">Standardmäßig geht Entity Framework Core davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="5f3d0-629">Bei von der Datenbank generierten Primärschlüsselwerten handelt es sich in der Regel um die beste Herangehensweise.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="5f3d0-630">Bei `Course`-Entitäten wird der Primärschlüssel vom Benutzer angegeben,</span><span class="sxs-lookup"><span data-stu-id="5f3d0-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="5f3d0-631">z.B. eine Kursnummer wie eine 1000er-Reihe für den Fachbereich Mathematik, eine 2000er-Reihe für den Fachbereich Englisch usw.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="5f3d0-632">Das `DatabaseGenerated`-Attribut kann ebenfalls zum Generieren von Standardwerten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="5f3d0-633">Die Datenbank kann beispielsweise automatisch ein Datenfeld generieren, um das Datum aufzuzeichnen, an dem eine Zeile erstellt oder aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="5f3d0-634">Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-635">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-636">Die Fremdschlüssel- und Navigationseigenschaften in der Entität `Course` stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="5f3d0-637">Ein Kurs ist einem Fachbereich zugeordnet, es gibt also eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="5f3d0-638">In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="5f3d0-639">Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="5f3d0-640">`CourseAssignment` wird [später](#many-to-many-relationships) erläutert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="5f3d0-641">Erstellen der Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-641">Create the Department entity</span></span>

![Entität „Department“](complex-data-model/_static/department-entity.png)

<span data-ttu-id="5f3d0-643">Erstellen Sie *Models/Department.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="5f3d0-644">Das Column-Attribut</span><span class="sxs-lookup"><span data-stu-id="5f3d0-644">The Column attribute</span></span>

<span data-ttu-id="5f3d0-645">Zuvor wurde das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="5f3d0-646">Im Code für die `Department`-Entität wird das `Column`-Attribut verwendet, um die Zuordnung des SQL-Datentyps zu ändern.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="5f3d0-647">Die `Budget`-Spalte wird mithilfe des SQL Server-Typs „money“ in der Datenbank definiert:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="5f3d0-648">Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-648">Column mapping is generally not required.</span></span> <span data-ttu-id="5f3d0-649">Entity Framework Core wählt üblicherweise den geeigneten SQL Server-Datentyp basierend auf dem CLR-Typ der Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="5f3d0-650">Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="5f3d0-651">Bei `Budget` wird eine Währung verwendet, und der Datentyp „money“ ist für Währungen besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-652">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-653">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="5f3d0-654">Ein Fachbereich kann einen Administrator besitzen oder nicht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="5f3d0-655">Bei einem Administrator handelt es sich immer um einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-655">An administrator is always an instructor.</span></span> <span data-ttu-id="5f3d0-656">Aus diesem Grund wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur `Instructor`-Entität hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="5f3d0-657">Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine `Instructor`-Entität:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="5f3d0-658">Das Fragezeichen (?) im vorangehenden Code gibt an, dass die Eigenschaft auf NULL festlegbar ist.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="5f3d0-659">Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="5f3d0-660">Hinweis: Gemäß der Konvention aktiviert Entity Framework Core das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="5f3d0-661">Das kaskadierende Delete kann zu Zirkelregeln für kaskadierende Deletes führen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="5f3d0-662">Durch Zirkelregeln für kaskadierende Deletes wird eine Ausnahme ausgelöst, wenn eine Migration hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="5f3d0-663">Beispielsweise dann, wenn die `Department.InstructorID`-Eigenschaft als nicht auf NULL festlegbar definiert wurde:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="5f3d0-664">Entity Framework Core konfiguriert eine Regel für kaskadierende Deletes, um den Fachbereich zu löschen, wenn ein Dozent gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="5f3d0-665">Das Löschen eines Fachbereichs in Folge des Löschens eines Dozenten stellt nicht das beabsichtigte Verhalten dar.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="5f3d0-666">Die folgende [Fluent-API](#fluent-api-alternative-to-attributes) würde eine Einschränkungsregel anstelle eines kaskadierenden Deletes festlegen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="5f3d0-667">Durch den vorangehenden Code werden kaskadierende Deletes für die Beziehung zwischen „Department“ und „Instructor“ deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="5f3d0-668">Aktualisieren der Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-668">Update the Enrollment entity</span></span>

<span data-ttu-id="5f3d0-669">Ein Enrollment-Datensatz gilt für einen Kurs, der von einem Studenten besucht wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-669">An enrollment record is for one course taken by one student.</span></span>

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="5f3d0-671">Aktualisieren Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="5f3d0-672">Fremdschlüssel- und Navigationseigenschaften</span><span class="sxs-lookup"><span data-stu-id="5f3d0-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="5f3d0-673">Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="5f3d0-674">Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="5f3d0-675">Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="5f3d0-676">n:n-Beziehungen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="5f3d0-677">Es besteht eine m:n-Beziehung zwischen der `Student`- und der `Course`-Entität.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="5f3d0-678">Die `Enrollment`-Entität fungiert in der Datenbank als m:n-Jointabelle *mit Nutzlast*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="5f3d0-679">„Mit Nutzlast“ bedeutet, dass die Tabelle `Enrollment` außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall der Primärschlüssel und `Grade`) zusätzliche Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="5f3d0-680">Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="5f3d0-681">(Diese Abbildung wurde mithilfe von [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) für EF 6.x generiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="5f3d0-682">Das Erstellen des Diagramms ist nicht Teil des Tutorials.)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-682">Creating the diagram isn't part of the tutorial.)</span></span>

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

<span data-ttu-id="5f3d0-684">Jede Beziehung weist an einem Ende „1“ und am anderen Ende „\*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="5f3d0-685">Wenn in der Tabelle `Enrollment` nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel (`CourseID` und `StudentID`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="5f3d0-686">Eine m:n-Jointabelle ohne Nutzlast wird manchmal als „reine Jointabelle“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="5f3d0-687">Zwischen den Entitäten `Instructor` und `Course` besteht eine m:n-Beziehung mithilfe einer reinen Jointabelle.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="5f3d0-688">Hinweis: Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="5f3d0-689">Weitere Informationen finden Sie unter [Many-to-many relationships in EF Core 2.0 (m:n-Beziehungen in Entity Framework Core 2.0)](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="5f3d0-690">Die Entität „CourseAssignment“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-690">The CourseAssignment entity</span></span>

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="5f3d0-692">Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="5f3d0-693">Beziehung zwischen „Instructor“ und „Course“</span><span class="sxs-lookup"><span data-stu-id="5f3d0-693">Instructor-to-Courses</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="5f3d0-695">Folgendes gilt für die m:n-Beziehung zwischen „Instructor“ und „Course“:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="5f3d0-696">Eine Jointabelle ist erforderlich, die durch eine Entitätenmenge dargestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="5f3d0-697">Diese muss eine reine Jointabelle (eine Tabelle ohne Nutzlast) sein.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="5f3d0-698">Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="5f3d0-699">Der Name der Jointabelle für „Instructor“ und „Course“ lautet mithilfe dieses Musters beispielsweise `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="5f3d0-700">Es wird jedoch empfohlen, einen Namen zu verwenden, der die Beziehung beschreibt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="5f3d0-701">Datenmodelle fangen einfach an und werden dann größer.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-701">Data models start out simple and grow.</span></span> <span data-ttu-id="5f3d0-702">Währenddessen erhalten Joins ohne Nutzlast häufig Nutzlasten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="5f3d0-703">Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, muss dieser nicht geändert werden, wenn die Jointabelle sich verändert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="5f3d0-704">Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="5f3d0-705">„Books“ und „Customers“ könnten beispielsweise über eine Joinentität namens „Ratings“ verknüpft werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="5f3d0-706">Bei der m:n-Beziehung zwischen „Instructor“ und „Course“ ist `CourseAssignment``CourseInstructor` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="5f3d0-707">Zusammengesetzte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5f3d0-707">Composite key</span></span>

<span data-ttu-id="5f3d0-708">Fremdschlüssel sind nicht auf NULL festlegbar.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-708">FKs are not nullable.</span></span> <span data-ttu-id="5f3d0-709">Die beiden Fremdschlüssel in `CourseAssignment` (`InstructorID` und `CourseID`) identifizieren zusammen jede Zeile der `CourseAssignment`-Tabelle eindeutig.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="5f3d0-710">`CourseAssignment` erfordert keinen dedizierten Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="5f3d0-711">Die Eigenschaften `InstructorID` und `CourseID` fungieren als zusammengesetzter Fremdschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="5f3d0-712">Zusammengesetzte Fremdschlüssel können für Entity Framework Core nur über die *Fluent-API* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="5f3d0-713">Im folgenden Abschnitt wird das Konfigurieren des zusammengesetzten Fremdschlüssels erläutert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="5f3d0-714">Durch den zusammengesetzten Schlüssel wird sichergestellt, dass:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-714">The composite key ensures:</span></span>

* <span data-ttu-id="5f3d0-715">Mehrere Zeilen für einen Kurs zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="5f3d0-716">Mehrere Zeilen für einen Dozenten zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="5f3d0-717">Mehrere Zeilen für denselben Dozenten und Kurs nicht zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="5f3d0-718">Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, wodurch Duplikate dieser Art möglich sind.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="5f3d0-719">So verhindern Sie solche Duplikate:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="5f3d0-720">Fügen Sie einen eindeutigen Index zu den Feldern für Fremdschlüssel hinzu, oder</span><span class="sxs-lookup"><span data-stu-id="5f3d0-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="5f3d0-721">konfigurieren Sie `Enrollment` mit einem zusammengesetzten Primärschlüssel, der `CourseAssignment` ähnelt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="5f3d0-722">Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="5f3d0-723">Aktualisieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="5f3d0-723">Update the DB context</span></span>

<span data-ttu-id="5f3d0-724">Fügen Sie folgenden hervorgehobenen Code zu *Data/SchoolContext.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-724">Add the following highlighted code to *Data/SchoolContext.cs* :</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="5f3d0-725">Durch den vorangehenden Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität `CourseAssignment` wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="5f3d0-726">Fluent-API-Alternativen für Attribute</span><span class="sxs-lookup"><span data-stu-id="5f3d0-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="5f3d0-727">Die `OnModelCreating`-Methode im vorangehenden Code verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="5f3d0-728">Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="5f3d0-729">Der [folgende Code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) veranschaulicht die Fluent-API beispielhaft:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="5f3d0-730">In diesem Tutorial wird die Fluent-API nur für die Datenbankzuordnung verwendet, die nicht mit Attributen erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="5f3d0-731">Die Fluent-API kann jedoch den Großteil der Regeln für Formatierung, Validierung und Zuordnung angeben, die mithilfe von Attributen festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="5f3d0-732">Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="5f3d0-733">Durch `MinimumLength` wird das Schema nicht geändert, sondern lediglich eine Validierungsregel für die mindestens erforderliche Länge angewendet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="5f3d0-734">Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="5f3d0-735">Attribute und die Fluent-API können gemeinsam verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="5f3d0-736">Es gibt einige Konfigurationen, die nur mit der Fluent-API vorgenommen werden können, z.B. das Angeben eines zusammengesetzten Primärschlüssels.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="5f3d0-737">Es gibt einige Konfigurationen, die nur mit Attributen (`MinimumLength`) vorgenommen werden können.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="5f3d0-738">Folgende Vorgehensweise wird für die Verwendung der Fluent-API oder von Attributen empfohlen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="5f3d0-739">Wählen Sie einen der beiden Ansätze aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="5f3d0-740">Verwenden Sie den ausgewählten Ansatz so konsistent wie möglich.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="5f3d0-741">Einige der in diesem Tutorial verwendeten Attribute werden für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="5f3d0-742">Nur für die Validierung (z.B. `MinimumLength`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="5f3d0-743">Nur für die Konfiguration von Entity Framework Core (z.B. `HasKey`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="5f3d0-744">Für die Validierung und die Konfiguration von Entity Framework (z.B. `[StringLength(50)]`)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="5f3d0-745">Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="5f3d0-746">Entitätsdiagramm mit angezeigten Beziehungen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="5f3d0-747">Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

<span data-ttu-id="5f3d0-749">Das vorherige Diagramm stellt Folgendes dar:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="5f3d0-750">Mehrere Linien für 1:n-Beziehungen (1:\*)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="5f3d0-751">Die Linie für die 1:0..1-Beziehung (1:0..1) zwischen den Entitäten `Instructor` und `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="5f3d0-752">Die Linie für die 0..1:n-Beziehung (0..1:\*) zwischen den Entitäten `Instructor` und `Department`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="5f3d0-753">Ausführen eines Seedings mit Testdaten für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="5f3d0-754">Aktualisieren Sie den Code in *Data/DbInitializer.cs* :</span><span class="sxs-lookup"><span data-stu-id="5f3d0-754">Update the code in *Data/DbInitializer.cs* :</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="5f3d0-755">Der vorangehende Code stellt Startwertdaten für die neuen Entitäten bereit.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="5f3d0-756">Durch diesen Code werden überwiegend neue Entitätsobjekte erstellt und Beispieldaten geladen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="5f3d0-757">Die Beispieldaten werden für Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-757">The sample data is used for testing.</span></span> <span data-ttu-id="5f3d0-758">Beispiele dazu, wie viele m:n-Jointabellen eingerichtet werden können, finden Sie unter `Enrollments` und `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="5f3d0-759">Hinzufügen einer Migration</span><span class="sxs-lookup"><span data-stu-id="5f3d0-759">Add a migration</span></span>

<span data-ttu-id="5f3d0-760">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-760">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="5f3d0-763">Der zuvor verwendete Befehl zeigt eine Warnung über möglichen Datenverlust an.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="5f3d0-764">Wenn der Befehl `database update` ausgeführt wird, wird folgende Fehlermeldung erzeugt:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="5f3d0-765">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="5f3d0-765">Apply the migration</span></span>

<span data-ttu-id="5f3d0-766">Da Sie nun über eine Datenbank verfügen, müssen Sie überlegen, wie zukünftig Änderungen an dieser vorgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="5f3d0-767">In diesem Tutorial werden zwei Vorgehensweisen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="5f3d0-768">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="5f3d0-769">[Anwenden der Migration auf die vorhandene Datenbank](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="5f3d0-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="5f3d0-770">Obwohl diese Methode komplexer und zeitaufwendiger ist, ist dies in der Praxis die bevorzugte Methode für Produktionsumgebungen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="5f3d0-771">**Hinweis:** Dies ist ein optionaler Abschnitt des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-771">**Note** : This is an optional section of the tutorial.</span></span> <span data-ttu-id="5f3d0-772">Sie können diesen Abschnitt überspringen und die Schritte zum Löschen und Neuerstellen durchführen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="5f3d0-773">Wenn Sie stattdessen die Schritte in diesem Abschnitt ausführen möchten, führen Sie nicht die Schritte zum Löschen und Neuerstellen aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5f3d0-774">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-774">Drop and re-create the database</span></span>

<span data-ttu-id="5f3d0-775">Durch den Code in der aktualisierten `DbInitializer`-Klasse werden Startwertdaten für die neuen Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="5f3d0-776">Löschen und aktualisieren Sie die Datenbank, um EF Core zum Erstellen einer neuen Datenbank zu zwingen:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3d0-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3d0-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3d0-778">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="5f3d0-779">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5f3d0-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5f3d0-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5f3d0-781">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="5f3d0-782">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="5f3d0-783">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="5f3d0-784">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-784">Run the app.</span></span> <span data-ttu-id="5f3d0-785">Durch das Ausführen der App wird die `DbInitializer.Initialize`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="5f3d0-786">Die `DbInitializer.Initialize`-Methode füllt die neue Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="5f3d0-787">Öffnen Sie die Datenbank im SSOX:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="5f3d0-788">Wenn der SSOX zuvor schon geöffnet war, klicken Sie auf die Schaltfläche **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="5f3d0-789">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-789">Expand the **Tables** node.</span></span> <span data-ttu-id="5f3d0-790">Die erstellten Tabellen werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-790">The created tables are displayed.</span></span>

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="5f3d0-792">Überprüfen Sie die Tabelle **CourseAssignment** :</span><span class="sxs-lookup"><span data-stu-id="5f3d0-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="5f3d0-793">Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment** , und klicken Sie auf **Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="5f3d0-794">Überprüfen Sie, ob die Tabelle **CourseAssignment** -Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-794">Verify the **CourseAssignment** table contains data.</span></span>

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="5f3d0-796">Anwenden der Migration auf die vorhandene Datenbank</span><span class="sxs-lookup"><span data-stu-id="5f3d0-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="5f3d0-797">Dieser Abschnitt ist optional.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-797">This section is optional.</span></span> <span data-ttu-id="5f3d0-798">Diese Schritte funktionieren nur, wenn Sie den vorherigen Abschnitt [Löschen und Neuerstellen der Datenbank](#drop) übersprungen haben.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="5f3d0-799">Wenn Migrationen mit vorhandenen Daten ausgeführt werden, gibt es möglicherweise Fremdschlüsseleinschränkungen, die durch die vorhandenen Daten nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="5f3d0-800">Bei Produktionsdaten müssen Schritte ausgeführt werden, um die vorhandenen Daten zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="5f3d0-801">In diesem Abschnitt ist ein Beispiel zum Beheben von Verstößen gegen die Fremdschlüsseleinschränkungen enthalten.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="5f3d0-802">Nehmen Sie diese Codeänderungen nicht vor, ohne zuvor eine Sicherung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="5f3d0-803">Nehmen Sie diese Codeänderungen nicht vor, wenn Sie den vorherigen Abschnitt abgeschlossen und die Datenbank aktualisiert haben.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="5f3d0-804">Die Datei *{timestamp}_ComplexDataModel.cs* enthält folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="5f3d0-805">Der vorangehende Code fügt den nicht auf NULL festlegbaren Fremdschlüssel `DepartmentID` zur Tabelle `Course` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="5f3d0-806">Die Datenbank aus dem vorherigen Tutorial enthält Zeilen in `Course` und kann daher nicht durch Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="5f3d0-807">So ermöglichen Sie die `ComplexDataModel`-Migration mit vorhandenen Daten:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="5f3d0-808">Ändern Sie den Code, um der neuen Spalte (`DepartmentID`) einen Standardnamen zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="5f3d0-809">Erstellen Sie einen Dummy-Fachbereich namens „Temp“, die als Standardfachbereich fungiert.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="5f3d0-810">Aufheben der Fremdschlüsseleinschränkungen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="5f3d0-811">Aktualisieren Sie die `Up`-Methode der `ComplexDataModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="5f3d0-812">Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="5f3d0-813">Kommentieren Sie die Codezeile aus, die die Spalte `DepartmentID` zur Tabelle `Course` hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="5f3d0-814">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-814">Add the following highlighted code.</span></span> <span data-ttu-id="5f3d0-815">Der neue Code folgt auf den Block `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="5f3d0-816">Aufgrund der zuvor durchgeführten Änderungen beziehen sich die vorhandenen `Course`-Zeilen auf den Fachbereich „Temp“, nachdem die Methode `ComplexDataModel` `Up` ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="5f3d0-817">Ein Produktions-App würde:</span><span class="sxs-lookup"><span data-stu-id="5f3d0-817">A production app would:</span></span>

* <span data-ttu-id="5f3d0-818">Code oder Skripts einfügen, um `Department`-Zeilen und verknüpfte `Course`-Zeilen zu den neuen `Department`-Zeilen hinzuzufügen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="5f3d0-819">Den Fachbereich „Temp“ nicht als Standardwert für `Course.DepartmentID` verwenden</span><span class="sxs-lookup"><span data-stu-id="5f3d0-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="5f3d0-820">Im folgenden Tutorial werden verknüpfte Daten behandelt.</span><span class="sxs-lookup"><span data-stu-id="5f3d0-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f3d0-821">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5f3d0-821">Additional resources</span></span>

* [<span data-ttu-id="5f3d0-822">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="5f3d0-823">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="5f3d0-824">[Zurück](xref:data/ef-rp/migrations)
> [Weiter](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="5f3d0-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
