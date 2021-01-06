# <a name="aspnet-core-health-check-sample"></a>Beispiel für die ASP.NET Core-Integritätsprüfung

Dieses Beispiel veranschaulicht die Verwendung von Middleware und Diensten für die Integritätsprüfung. Dieses Beispiel veranschaulicht das Szenario aus dem Thema [Integritätsprüfungen in ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).

Um die Beispiel-App für ein im Thema beschriebenes Szenario auszuführen, verwenden Sie den Befehl [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) aus dem Ordner des Projekts in einer Befehlsshell. Übergeben Sie einen Schalter für das Szenario, das Sie untersuchen möchten. Die App verwendet standardmäßig die `basic`-Konfiguration, wenn kein Schalter in `dotnet run` angegeben wird.

| Szenario                                               | Befehl für die Beispiel-App               | BESCHREIBUNG |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Grundlegender Integritätstest (Standard)                           | `dotnet run --scenario basic`    | Bestätigt, dass die App HTTP-Anforderungen verarbeiten kann. |
| Datenbanktest                                         | `dotnet run --scenario db`       | Überprüft die Verbindung mit der SQL Server-Datenbank. Im Abschnitt [Datenbanktest](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) des Themas finden Sie Anweisungen dazu. |
| Tests auf Bereitschaft/Lebendigkeit                              | `dotnet run --scenario liveness` | Überprüft den Status einer Live-App (*Lebendigkeit*) im Vergleich zum Status einer App, die auf die Liveschaltung vorbereitet wird (*Bereitschaft*). |
| Metrikbasierter Test (Arbeitsspeicher)/<br>benutzerdefinierter Antwortwriter | `dotnet run --scenario writer`   | Überprüft die Arbeitsspeichernutzung und schreibt benutzerdefinierten JSON-Code, wenn der Integritätsendpunkt geprüft wird. |
| Filtern nach Port                                         | `dotnet run --scenario port`     | Filtert Integritätsprüfungen nach einem bestimmten Port. Im Abschnitt [Filtern nach Port](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) des Themas finden Sie Anweisungen dazu. |

Die Szenarien für Datenbanktest und Portfilterung erfordern zusätzliche Konfigurationsschritte. Details finden Sie im Thema [Integritätsprüfungen](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).
