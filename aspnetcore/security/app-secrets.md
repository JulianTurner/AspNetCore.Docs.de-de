---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc, contperfq2
ms.date: 11/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035852"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument wird erläutert, wie sensible Daten für eine ASP.net Core-App auf einem Entwicklungs Computer verwaltet werden. Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode. Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden. Geheimnisse sollten nicht mit der APP bereitgestellt werden. Stattdessen sollte auf die Produktionsgeheimnisse über ein kontrolliertes Mittel wie Umgebungsvariablen oder Azure Key Vault zugegriffen werden. Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.

Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *appsettings.json* Datei mit dem Schlüssel enthalten `DefaultConnection` . Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden. In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.

> [!WARNING]
> Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert. Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden. Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts. In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel. App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert. Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden. Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Dies ist nur für Entwicklungszwecke vorgesehen. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>So funktioniert das Secret Manager-Tool

Das Secret Manager-Tool verbirgt Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen. Die Werte werden in einer JSON-Datei im Benutzerprofil Ordner des lokalen Computers gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` in der Projektdatei angegebenen Wert.

Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.

## <a name="enable-secret-storage"></a>Aktivieren der geheimen Speicherung

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Das Secret Manager-Tool enthält einen `init` Befehl in .net Core SDK 3.0.100 oder höher. Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet user-secrets init
```

Mit dem vorangehenden Befehl wird ein- `UserSecretsId` Element in einer `PropertyGroup` der Projektdatei hinzugefügt. Standardmäßig ist der innere Text von `UserSecretsId` eine GUID. Der innere Text ist willkürlich, aber für das Projekt eindeutig.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus. Diese Geste fügt `UserSecretsId` der Projektdatei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.

## <a name="set-a-secret"></a>Festlegen eines Geheimnisses

Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht. Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` . Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.

Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden. Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die Projektdatei vorhanden ist. Beispiel:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Vereinfachen der JSON-Struktur in Visual Studio

Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor. Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare. Beispiel:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` . Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert. Die geänderte Datei ähnelt der folgenden JSON-Datei:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer geheimer Schlüssel

Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` . Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Auf einen geheimen Schlüssel zugreifen

Um auf ein Geheimnis zuzugreifen, führen Sie die folgenden Schritte aus:

1. [Registrieren der Konfigurations Quelle für Benutzer Geheimnisse](#register-the-user-secrets-configuration-source)
1. [Lesen des geheimen Schlüssels über die Konfigurations-API](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a>Registrieren der Konfigurations Quelle für Benutzer Geheimnisse

Der [Konfigurations Anbieter](/dotnet/core/extensions/configuration-providers) für Benutzer Geheimnisse registriert die geeignete Konfigurations Quelle bei der .net- [Konfigurations-API](xref:fundamentals/configuration/index).

Die Konfigurations Quelle für Benutzer Geheimnisse wird automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> . `CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> ist <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in Aufrufen <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> . `AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a>Lesen des geheimen Schlüssels über die Konfigurations-API

Wenn die Konfigurations Quelle "Benutzer Geheimnisse" registriert ist, kann die .NET-Konfigurations-API die geheimen Schlüssel lesen. Die [Konstruktorinjektion](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) kann verwendet werden, um Zugriff auf die .NET-Konfigurations-API zu erhalten. Beachten Sie die folgenden Beispiele, um den Schlüssel zu lesen `Movies:ServiceApiKey` :

**Startup-Klasse:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

**Razor Seiten Modell der Seite:**

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

Weitere Informationen finden Sie unter [Zugriffs Konfiguration in Start](xref:fundamentals/configuration/index#access-configuration-in-startup) -und [Zugriffs Konfiguration in Razor Seiten](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).

## <a name="map-secrets-to-a-poco"></a>Zuordnen von Geheimnissen zu einem poco

Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der .NET-Konfigurations-API. Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zeichen folgen Ersetzung mit geheimen Schlüsseln

Das Speichern von Kenn Wörtern im Klartext ist unsicher. Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *appsettings.json* ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appsettings.json* . Beispiel:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Auflisten der geheimen Schlüssel

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets list
```

Die folgende Ausgabe wird angezeigt:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan*.

## <a name="remove-a-single-secret"></a>Entfernen eines einzelnen geheimen Schlüssels

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` zeigt die folgende Meldung an:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Alle geheimen Schlüssel entfernen

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets clear
```

Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:

```json
{}
```

Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf Benutzer Geheimnisse aus IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Dokument wird erläutert, wie sensible Daten für eine ASP.net Core-App auf einem Entwicklungs Computer verwaltet werden. Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode. Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden. Geheimnisse sollten nicht mit der APP bereitgestellt werden. Stattdessen sollte auf die Produktionsgeheimnisse über ein kontrolliertes Mittel wie Umgebungsvariablen oder Azure Key Vault zugegriffen werden. Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden. Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.

Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist. Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *appsettings.json* Datei mit dem Schlüssel enthalten `DefaultConnection` . Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert. Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden. In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.

> [!WARNING]
> Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert. Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden. Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Secret Manager

Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts. In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel. App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert. Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden. Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt

> [!WARNING]
> Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden. Dies ist nur für Entwicklungszwecke vorgesehen. Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.

## <a name="how-the-secret-manager-tool-works"></a>So funktioniert das Secret Manager-Tool

Das Secret Manager-Tool verbirgt Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden. Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen. Die Werte werden in einer JSON-Datei im Benutzerprofil Ordner des lokalen Computers gespeichert:

# <a name="windows"></a>[Windows](#tab/windows)

Dateisystempfad:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Dateisystempfad:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` in der Projektdatei angegebenen Wert.

Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt. Diese Implementierungsdetails können sich ändern. Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.

## <a name="enable-secret-storage"></a>Aktivieren der geheimen Speicherung

Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.

Zum Verwenden von Benutzer Geheimnissen definieren Sie ein- `UserSecretsId` Element innerhalb einer `PropertyGroup` der Projektdatei. Der innere Text von `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig. Entwickler generieren in der Regel eine GUID für das `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus. Diese Geste fügt `UserSecretsId` der Projektdatei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.

## <a name="set-a-secret"></a>Festlegen eines Geheimnisses

Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht. Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` . Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.

Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden. Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die Projektdatei vorhanden ist. Beispiel:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Vereinfachen der JSON-Struktur in Visual Studio

Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor. Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare. Beispiel:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` . Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert. Die geänderte Datei ähnelt der folgenden JSON-Datei:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Festlegen mehrerer geheimer Schlüssel

Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` . Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .

# <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Auf einen geheimen Schlüssel zugreifen

Die [Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Benutzer Geheimnisse.

Wenn das Projekt .NET Framework als Ziel hat, installieren Sie die [Microsoft.Extensions.Config-urung. Usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) -nuget-Paket.

In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> . `CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> ist <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im- `Startup` Konstruktor aufrufen. `AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Benutzer Geheimnisse können über die .NET-Konfigurations-API abgerufen werden:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Zuordnen von Geheimnissen zu einem poco

Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der .NET-Konfigurations-API. Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Zeichen folgen Ersetzung mit geheimen Schlüsseln

Das Speichern von Kenn Wörtern im Klartext ist unsicher. Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *appsettings.json* ein Kennwort für den angegebenen Benutzer enthalten:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern. Beispiel:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appsettings.json* . Beispiel:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Auflisten der geheimen Schlüssel

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets list
```

Die folgende Ausgabe wird angezeigt:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan*.

## <a name="remove-a-single-secret"></a>Entfernen eines einzelnen geheimen Schlüssels

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Alle geheimen Schlüssel entfernen

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:

```dotnetcli
dotnet user-secrets clear
```

Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:

```json
{}
```

Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Informationen zum Zugriff auf Benutzer Geheimnisse aus IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end