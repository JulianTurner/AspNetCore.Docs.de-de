<a name="csc"></a>

Mit der folgenden `ConfigureServices`-Methode können Sie Dienste registrieren und Optionen konfigurieren:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

Ähnliche Registrierungsgruppen können in eine Erweiterungsmethode verschoben werden, um Dienste zu registrieren. Die Konfigurationsdienste werden beispielsweise folgender Klasse hinzugefügt:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

Die verbleibenden Dienste werden in einer ähnlichen Klasse registriert. Die folgende `ConfigureServices`-Methode verwendet die neuen Erweiterungsmethoden, um die Dienste zu registrieren:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_Hinweis:_** Jede `services.Add{GROUP_NAME}`-Erweiterungsmethode fügt Dienste hinzu und konfiguriert diese möglicherweise. Beispielsweise fügt <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> den MVC-Controller für Dienste mit den erforderlichen Ansichten hinzu, und <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> fügt die für Razor Pages benötigten Dienste hinzu. Es wird empfohlen, dass Apps dieser Namenskonvention folgen. Platzieren Sie Erweiterungsmethoden im Namespace <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, um Gruppen von Dienstregistrierungen zu kapseln.
