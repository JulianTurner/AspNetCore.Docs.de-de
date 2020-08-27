---
title: Konfiguration in ASP.NET Core
author: rick-anderson
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
no-loc:
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
uid: fundamentals/configuration/index
ms.openlocfilehash: ed40800f3e363cc3fd6f57492d76a42095e5b97b
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712557"
---
# <a name="configuration-in-aspnet-core"></a>Konfiguration in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Die Konfiguration in ASP.NET Core erfolgt mithilfe eines oder mehrerer [Konfigurationsanbieter](#cp). Konfigurationsanbieter lesen Konfigurationsdaten aus Schlüssel-Wert-Paaren unter Verwendung verschiedener Konfigurationsquellen:

* Einstellungsdateien, z. B. *appsettings.json*
* Umgebungsvariablen
* Azure Key Vault
* Azure App Configuration
* Befehlszeilenargumente
* Benutzerdefinierte Anbieter (installiert oder erstellt)
* Verzeichnisdateien
* Speicherinterne .NET Objekte

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

<a name="default"></a>

## <a name="default-configuration"></a>Standardkonfiguration

ASP.NET Core-Web-Apps, die mit [dotnet new](/dotnet/core/tools/dotnet-new) oder Visual Studio erstellt wurden, generieren den folgenden Code:

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:

1. [ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  Fügt eine vorhandene `IConfiguration` als Quelle hinzu. Im Fall einer Standardkonfiguration wird die [Host](#hvac)-Konfiguration hinzugefügt und als erste Quelle für die _App-_ Konfiguration festgelegt.
1. [appsettings.json](#appsettingsjson) mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).
1. *appsettings.* `Environment` *.json* mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider). Beispielsweise *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.
1. [App-Geheimnisse](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird
1. Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden
1. Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line) verwenden

Später hinzugefügte Konfigurationsanbieter überschreiben vorherige Schlüsseleinstellungen. Wenn beispielsweise `MyKey` sowohl unter *appsettings.json* als auch unter Umgebung festgelegt wird, wird der Umgebungswert verwendet. Bei Verwendung der Standardkonfigurationsanbieter überschreibt der [Befehlszeilen-Konfigurationsanbieter](#clcp) alle anderen Anbieter.

Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter [Standardeinstellungen für den Generator](xref:fundamentals/host/generic-host#default-builder-settings).

Im folgenden Code werden die aktivierten Konfigurationsanbieter in der Reihenfolge angezeigt, in der sie hinzugefügt wurden:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a>appsettings.json

Sehen Sie sich die nachfolgende Datei *appsettings.json* an:

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Der Standard-<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration in der folgenden Reihenfolge:

1. *appsettings.json*
1. *appsettings.* `Environment` *.json*: Beispielsweise die Dateien *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*. Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen. Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

*appsettings*.`Environment`.*json*-Werte überschreiben Schlüssel in *appsettings.json*. Standardmäßig sind dies z. B.:

* In der Entwicklung überschreibt die *appsettings*.***Development***.*json*-Konfiguration in *appsettings.json* gefundene Werte.
* In der Produktion überschreibt die *appsettings*.***Production***.*json*-Konfiguration in *appsettings.json* gefundene Werte. Dies ist beispielsweise bei der Bereitstellung der App in Azure der Fall.

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a>Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster

[!INCLUDE[](~/includes/bind.md)]

Wenn die [Standard](#default)-Konfiguration verwendet wird, werden die Dateien *appsettings.json* und *appsettings.* `Environment` *.json* mit [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) aktiviert. Änderungen an den Dateien *appsettings.json* und *appsettings.* `Environment` *.json* ***nach*** dem Start der App werden vom [JSON-Konfigurationsanbieter](#jcp) gelesen.

Weitere Informationen zum Hinzufügen zusätzlicher JSON-Konfigurationsdateien finden Sie unter [JSON-Konfigurationsanbieter](#jcp) in diesem Dokument.

## <a name="combining-service-collection"></a>Kombinieren von Dienstsammlungen

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a>Sicherheits- und Secret-Manager

Richtlinien für Konfigurationsdaten:

* Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien. Der [Secret Manager](xref:security/app-secrets) kann zum Speichern von Geheimnissen in der Entwicklungsumgebung verwendet werden.
* Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.
* Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.

[Standardmäßig](#default) liest [Secret Manager](xref:security/app-secrets) Konfigurationseinstellungen nach dem Lesen von *appsettings.json* und *appsettings.* `Environment` *.json*.

Weitere Informationen zum Speichern von Kennwörtern oder anderen vertraulichen Daten:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>:  Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten. Der Secret Manager verwendet den [Dateikonfigurationsanbieter](#fcp), um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise. Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.

<a name="evcp"></a>

## <a name="environment-variables"></a>Umgebungsvariablen

Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> die Konfiguration aus Schlüssel-Wert-Paaren der Umgebungsvariablen, nachdem *appsettings.json*, *appsettings.* `Environment` *.json* und [Secret Manager](xref:security/app-secrets) gelesen wurden. Daher überschreiben aus der Umgebung gelesene Schlüsselwerte Werte, die aus *appsettings.json*, *appsettings.* `Environment` *.json* und Secret Manager gelesen wurden.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Die folgenden `set`-Befehle:

* Legen die Umgebungsschlüssel und -werte des [vorangehenden Beispiels](#appsettingsjson) unter Windows fest.
* Testen die Einstellungen bei Verwendung des [Beispieldownloads](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample). Der `dotnet run`-Befehl muss im Projektverzeichnis ausgeführt werden.

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

Die obigen Umgebungseinstellungen:

* Werden nur in Prozessen festgelegt, die über das Befehlsfenster gestartet werden, in dem sie festgelegt wurden.
* Werden nicht von Browsern gelesen, die mit Visual Studio gestartet wurden.

Die folgenden [setx](/windows-server/administration/windows-commands/setx)-Befehle können zum Festlegen der Umgebungsschlüssel und -werte unter Windows verwendet werden. Anders als `set` werden `setx`-Einstellungen beibehalten. `/M` legt die Variable in der Systemumgebung fest. Wenn der `/M`-Schalter nicht verwendet wird, wird eine Benutzerumgebungsvariable festgelegt.

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

Testen Sie wie folgt, ob die obigen Befehle *appsettings.json* und *appsettings.* `Environment` *.json* überschreiben:

* Mit Visual Studio: Beenden Sie Visual Studio, und starten Sie dann Visual Studio neu.
* Mit der Befehlszeilenschnittstelle: Starten Sie ein neues Befehlsfenster, und geben Sie `dotnet run` ein.

Rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> mit einer Zeichenfolge auf, um ein Präfix für Umgebungsvariablen anzugeben:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

Für den Code oben gilt:

* `config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` wird nach den [Standardkonfigurationsanbietern](#default) hinzugefügt. Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).
* Umgebungsvariablen, die mit dem `MyCustomPrefix_`-Präfix festgelegt wurden, überschreiben die [Standardkonfigurationsanbieter](#default). Dies schließt Umgebungsvariablen ohne das Präfix ein.

Das Präfix wird beim Lesen der Schlüssel-Wert-Paare der Konfiguration entfernt.

Mit den folgenden Befehlen wird das benutzerdefinierte Präfix getestet:

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

Die [Standardkonfiguration](#default) lädt Umgebungsvariablen und Befehlszeilenargumente, die das Präfix `DOTNET_` und `ASPNETCORE_` aufweisen. Die Präfixe `DOTNET_` und `ASPNETCORE_` werden von ASP.NET Core für die [Host- und App-Konfiguration](xref:fundamentals/host/generic-host#host-configuration), jedoch nicht für die Benutzerkonfiguration verwendet. Weitere Informationen zur Host- und App-Konfiguration finden Sie unter [.NET Generic Host](xref:fundamentals/host/generic-host).

Wählen Sie in [Azure App Service](https://azure.microsoft.com/services/app-service/) auf der Seite **Einstellungen > Konfiguration** die Option **Neue Anwendungseinstellung** aus. Anwendungseinstellungen von Azure App Service werden:

* Im Ruhezustand verschlüsselt und über einen verschlüsselten Kanal übermittelt.
* Als Umgebungsvariablen verfügbar gemacht.

Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Informationen zu Azure-Datenbankverbindungszeichenfolgen finden Sie unter [Präfixe für Verbindungszeichenfolgen](#constr).

### <a name="environment-variables-set-in-launchsettingsjson"></a>In „launchSettings.json“ festgelegte Umgebungsvariablen

Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.

<a name="clcp"></a>

## <a name="command-line"></a>Befehlszeile

Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments nach den folgenden Konfigurationsquellen:

* Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.
* [App-Geheimnisse (Secret Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.
* Umgebungsvariablen.

[Standardmäßig](#default) überschreiben in der Befehlszeile festgelegte Konfigurationswerte die Konfigurationswerte, die mit allen anderen Konfigurationsanbietern festgelegt wurden.

### <a name="command-line-arguments"></a>Befehlszeilenargumente

Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `=` fest:

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `/` fest:

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `--` fest:

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

Der Schlüsselwert:

* Muss auf `=` folgen, oder der Schlüssel muss das Präfix `--` oder `/` aufweisen, wenn der Wert auf ein Leerzeichen folgt.
* Ist nicht erforderlich, wenn `=` verwendet wird. Beispielsweise `MySetting=`.

Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare des Befehlszeilenarguments, die `=` verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.

### <a name="switch-mappings"></a>Switchmappings

Switchmappings erlauben das Angeben einer Logik zum Ersetzen von **Schlüsselnamen**. Stellen Sie ein Wörterbuch mit Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereit.

Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft. Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben. Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.

Regeln für Schlüssel von Switchmappingwörterbüchern:

* Switches müssen mit `-` oder `--` beginnen.
* Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.

Wenn Sie ein Switchmappingwörterbuch verwenden möchten, übergeben Sie es an den `AddCommandLine`-Abruf:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

Der folgende Code zeigt die Schlüsselwerte für die ersetzten Schlüssel:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

Mit dem folgenden Befehl kann die Schlüsselersetzung getestet werden:

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden. Der `AddCommandLine`-Aufruf der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden. Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der `AddCommandLine`-Methode der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.

## <a name="hierarchical-configuration-data"></a>Hierarchische Konfigurationsdaten

Die Konfigurations-API liest hierarchische Konfigurationsdaten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.

Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *appsettings.json*:

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt einige der Konfigurationseinstellungen:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Die bevorzugte Methode zum Lesen hierarchischer Konfigurationsdaten ist die Verwendung des Optionsmusters. Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten](#optpat) in diesem Dokument.

Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden. Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection) beschrieben.

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a>Konfigurationsschlüssel und -werte

Konfigurationsschlüssel:

* Groß-/Kleinschreibung wird nicht beachtet. Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.
* Wenn ein Schlüssel und ein Wert in mehreren Konfigurationsanbietern festgelegt ist, wird der Wert des zuletzt hinzugefügten Anbieters verwendet. Weitere Informationen finden Sie unter [Standardkonfiguration](#default).
* Hierarchische Schlüssel
  * Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.
  * In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen. Ein doppelter Unterstrich (`__`) wird von allen Plattformen unterstützt und automatisch in einen Doppelpunkt (`:`) umgewandelt.
  * In Azure Key Vault verwenden hierarchische Schlüssel `--` als Trennzeichen. Der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) ersetzt `--` automatisch durch `:`, wenn die Geheimnisse in die Konfiguration der App geladen werden.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln. Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#boa) beschrieben.

Konfigurationswerte:

* Sind Zeichenfolgen.
* NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.

<a name="cp"></a>

## <a name="configuration-providers"></a>Konfigurationsanbieter

Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.

| Anbieter | Bereitstellung der Konfiguration über |
| -------- | ----------------------------------- |
| [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) | Azure Key Vault |
| [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration |
| [Befehlszeilen-Konfigurationsanbieter](#clcp) | Befehlszeilenparameter |
| [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle |
| [Umgebungsvariablen-Konfigurationsanbieter](#evcp) | Umgebungsvariablen |
| [Dateikonfigurationsanbieter](#file-configuration-provider) | INI-, JSON- und XML-Dateien |
| [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien |
| [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen |
| [Secret Manager](xref:security/app-secrets)  | Datei im Benutzerprofilverzeichnis |

Konfigurationsquellen werden in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind. Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.

Eine typische Konfigurationsanbietersequenz ist:

1. *appsettings.json*
1. *appsettings*.`Environment`.*json*
1. [Secret Manager](xref:security/app-secrets)
1. Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden
1. Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden

In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle hinzugefügt, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.

Die obige Sequenz von Anbietern wird in der [Standardkonfiguration](#default) verwendet.

<a name="constr"></a>

### <a name="connection-string-prefixes"></a>Präfixe für Verbindungszeichenfolgen

Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen. Diese Verbindungszeichenfolgen sind beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung beteiligt. Umgebungsvariablen mit den in der Tabelle aufgeführten Präfixen werden mit der [Standardkonfiguration](#default) in die App geladen bzw. wenn kein Präfix für `AddEnvironmentVariables` bereitgestellt wird.

| Präfix für Verbindungszeichenfolgen | Anbieter |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:

* Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.
* Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).

| Umgebungsvariablenschlüssel | Konvertierter Konfigurationsschlüssel | Anbieterkonfigurationseintrag                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `System.Data.SqlClient`  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a>Dateikonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem. Die folgenden Konfigurationsanbieter leiten sich vom `FileConfigurationProvider` ab:

* [INI-Konfigurationsanbieter](#ini-configuration-provider)
* [JSON-Konfigurationsanbieter](#jcp)
* [XML-Konfigurationsanbieter](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.

Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

Im obigen Code werden Einstellungen in den Dateien *MyIniConfig.ini* und *MyIniConfig*.`Environment`.*ini* überschrieben durch Einstellungen im:

* [Umgebungsvariablen-Konfigurationsanbieter](#evcp)
* [Befehlszeilen-Konfigurationsanbieter](#clcp)

Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyIniConfig.ini*:

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a>JSON-Konfigurationsanbieter

Der <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.

Überladungen können Folgendes angeben:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird

Betrachten Sie folgenden Code:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

Der vorangehende Code:

* Konfiguriert den JSON-Konfigurationsanbieter, sodass dieser die Datei *MyConfig.json* mit den folgenden Optionen lädt:
  * `optional: true`: Die Datei ist optional.
  * `reloadOnChange: true` : Die Datei wird erneut geladen, wenn Änderungen gespeichert werden.
* Liest die [Standardkonfigurationsanbieter](#default) vor der Datei *MyConfig.json*. Einstellungen in der Datei *MyConfig.json* überschreiben die Einstellung in den Standardkonfigurationsanbietern, einschließlich des [Umgebungsvariablen-Konfigurationsanbieters](#evcp) und des [Befehlszeilen-Konfigurationsanbieters](#clcp).

In der Regel möchten Sie ***nicht***, dass eine benutzerdefinierte JSON-Datei Werte überschreibt, die im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) festgelegt sind.

Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

Im obigen Code gilt für Einstellungen in den Dateien *MyConfig.json* und *MyConfig*.`Environment`.*json*:

* Sie überschreiben Einstellungen in den Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.
* Sie werden durch Einstellungen im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) überschrieben.

Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyConfig.json*:

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a>XML-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.

Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

Im obigen Code werden Einstellungen in den Dateien *MyXMLFile.xml* und *MyXMLFile*.`Environment`.*xml* überschrieben durch Einstellungen im:

* [Umgebungsvariablen-Konfigurationsanbieter](#evcp)
* [Befehlszeilen-Konfigurationsanbieter](#clcp)

Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyXMLFile.xml*:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

Der folgende Code liest die vorherige Konfigurationsdatei und zeigt die Schlüssel und Werte an:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

Mit Attributen können Werte bereitgestellt werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Schlüssel-pro-Datei-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare. Der Schlüssel ist der Dateiname. Der Wert enthält den Inhalt der Datei. Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarios verwendet.

Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf. Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.

Überladungen geben Folgendes an:

* Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert
* Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis

Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet. Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a>Speicherkonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.

Der folgende Code fügt eine Arbeitsspeichersammlung zum Konfigurationssystem hinzu:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt die vorherigen Konfigurationseinstellungen an:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

Im obigen Code wird `config.AddInMemoryCollection(Dict)` nach den [Standardkonfigurationsanbietern](#default) hinzugefügt. Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).

Ein weiteres Beispiel für die Verwendung von `MemoryConfigurationProvider` finden Sie unter [Binden eines Arrays](#boa).

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

Wenn im obigen Code `NumberKey` nicht in der Konfiguration gefunden wird, wird der Standardwert `99` verwendet.

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren und Exists

Beachten Sie für die nachstehenden Beispiele die folgende Datei *MySubsection.json*:

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

Der folgende Code fügt *MySubsection.json* zu den Konfigurationsanbietern hinzu:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) gibt einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel zurück.

Der folgende Code gibt Werte für `section1` zurück:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

Der folgende Code gibt Werte für `section2:subsection0` zurück:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

`GetSection` gibt nie `null` zurück. Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.

Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt. Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.

### <a name="getchildren-and-exists"></a>GetChildren und Exists

Mit dem folgenden Code wird [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) abgerufen, und es werden Werte für `section2:subsection0` zurückgegeben:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

Der obige Code ruft [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) auf, um zu überprüfen, ob der Abschnitt vorhanden ist:

 <a name="boa"></a>

## <a name="bind-an-array"></a>Binden eines Arrays

[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) unterstützt das Binden von Arrays an Objekte mithilfe von Arrayindizes in Konfigurationsschlüsseln. Jedes Arrayformat, das ein numerisches Schlüsselsegment verfügbar macht, kann ein Array an ein [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Klassenarray binden.

Beachten Sie *MyArray.json* aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

Der folgende Code fügt *MyArray.json* zu den Konfigurationsanbietern hinzu:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

Der folgende Code liest die Konfiguration und zeigt die Werte an:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Der obige Code erzeugt die folgende Ausgabe:

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

In der obigen Ausgabe hat Index 3 den Wert `value40`, der `"4": "value40",` in *MyArray.json* entspricht. Die gebundenen Arrayindizes sind kontinuierlich und nicht an den Konfigurationsschlüsselindex gebunden. Der Konfigurationsbinder ist nicht in der Lage, NULL-Werte zu binden oder NULL-Einträge in gebundenen Objekten zu erstellen.

Mit dem folgenden Code wird die `array:entries`-Konfiguration mit der <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode geladen:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

Der folgende Code liest die Konfiguration im `arrayDict` `Dictionary` und zeigt die Werte an:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Der obige Code erzeugt die folgende Ausgabe:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`. Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet. Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.

Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`-Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das Schlüssel-Wert-Paar von Index &num;3 liest. Beachten Sie die folgende Datei *Value3.json* aus dem Beispieldownload:

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

Der folgende Code enthält die Konfiguration für *Value3.json* und das `arrayDict` `Dictionary`:

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

Der folgende Code liest die obige Konfiguration und zeigt die Werte an:

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

Der obige Code erzeugt die folgende Ausgabe:

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.

## <a name="custom-configuration-provider"></a>Benutzerdefinierter Konfigurationsanbieter

Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.

Der Anbieter weist die folgenden Merkmale auf:

* Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet. Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.
* Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration. Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.
* Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.

Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist. Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a>Zugriffskonfiguration beim Start

Der folgende Code zeigt Konfigurationsdaten in `Startup`-Methoden an:

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-no-locrazor-pages"></a>Zugriffskonfiguration in Razor Pages

Der folgende Code zeigt Konfigurationsdaten auf einer Razor-Seite an:

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

Im folgenden Code wird `MyOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Das folgende Markup verwendet die [`@inject`](xref:mvc/views/razor#inject) Razor Direktive, um die Optionswerte aufzulösen und anzuzeigen:

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a>Zugriffskonfiguration in einer MVC-Ansichtsdatei

Der folgende Code zeigt Konfigurationsdaten in einer MVC-Ansicht an:

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a>Konfigurieren von Optionen mit einem Delegaten

In einem Delegaten konfigurierte Optionen überschreiben die in den Konfigurationsanbietern festgelegten Werte.

Das Konfigurieren von Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.

Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt. Er verwendet einen Delegaten, um Werte für `MyOptions` zu konfigurieren:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

Im folgenden Code werden die Optionswerte angezeigt:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` in *appsettings.json* angegeben und anschließend vom konfigurierten Delegaten überschrieben.

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a>Hostkonfiguration und App-Konfiguration im Vergleich

Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet. Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer. Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert. Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten. Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.

<a name="dhc"></a>

## <a name="default-host-configuration"></a>Standardhostkonfiguration

Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).

* Die Konfiguration des Hosts wird durch Folgendes festgelegt:
  * Umgebungsvariablen mit dem Präfix `DOTNET_` (z. B. `DOTNET_ENVIRONMENT`), die den [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables) verwenden. Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.
  * Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden
* Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):
  * Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.
  * Fügen Sie Middleware zum Filtern von Hosts hinzu.
  * Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.
  * Aktivieren Sie die IIS-Integration.

## <a name="other-configuration"></a>Sonstige Konfiguration

Dieses Thema bezieht sich nur auf *App-Konfigurationen*. Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:

* *launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:
  * In <xref:fundamentals/environments#development>.
  * In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.
* *web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.

Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="add-configuration-from-an-external-assembly"></a>Hinzufügen von Konfigurationen aus einer externen Assembly

Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Quellcode der Konfiguration](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden. Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:

* Azure Key Vault
* Azure App Configuration
* Befehlszeilenargumente
* Benutzerdefinierte Anbieter (installiert oder erstellt)
* Verzeichnisdateien
* Umgebungsvariablen
* Speicherinterne .NET Objekte
* Einstellungsdateien

Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.

Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:

```csharp
using Microsoft.Extensions.Configuration;
```

Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte. Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>Hostkonfiguration und App-Konfiguration im Vergleich

Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet. Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer. Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert. Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten. Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.

## <a name="other-configuration"></a>Sonstige Konfiguration

Dieses Thema bezieht sich nur auf *App-Konfigurationen*. Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:

* *launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:
  * In <xref:fundamentals/environments#development>.
  * In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.
* *web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.

## <a name="default-configuration"></a>Standardkonfiguration

Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf. `CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:

Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden. Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).

* Die Konfiguration des Hosts wird durch Folgendes festgelegt:
  * Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider). Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.
  * Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).
* Die App-Konfiguration wird durch Folgendes festgelegt:
  * *appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).
  * *appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).
  * [Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.
  * Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).
  * Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).

## <a name="security"></a>Sicherheit

Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:

* Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.
* Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.
* Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.

Weitere Informationen finden Sie unter den folgenden Themen:

* <xref:fundamentals/environments>
* <xref:security/app-secrets>: Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten. Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern. Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise. Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.

## <a name="hierarchical-configuration-data"></a>Hierarchische Konfigurationsdaten

Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.

Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten. Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden. Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.

## <a name="conventions"></a>Konventionen

### <a name="sources-and-providers"></a>Quellen und Anbieter

Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.

Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden. Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.

<xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung. <xref:Microsoft.Extensions.Configuration.IConfiguration> kann in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller> von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.

In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.

### <a name="keys"></a>Tasten

Konfigurationsschlüssel entsprechen den folgenden Konventionen:

* Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet. Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.
* Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet. Weitere Informationen zu doppelten JSON-Schlüsseln finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/extensions/issues/2381).
* Hierarchische Schlüssel
  * Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.
  * In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen. Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.
  * In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen. Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln. Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.

### <a name="values"></a>Werte

Konfigurationswerte entsprechen den folgenden Konventionen:

* Die Werte sind Zeichenfolgen.
* NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.

## <a name="providers"></a>Anbieter

Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.

| Anbieter | Bereitstellung der Konfiguration über&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*) | Azure Key Vault |
| [Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation) | Azure App Configuration |
| [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter |
| [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle |
| [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen |
| [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) |
| [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien |
| [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen |
| [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*) | Datei im Benutzerprofilverzeichnis |

Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind. Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet. Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.

Eine typische Konfigurationsanbietersequenz ist:

1. Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)
1. Umgebungsvariablen
1. Befehlszeilenargumente

In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.

Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren. Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).

## <a name="configure-the-host-builder-with-useconfiguration"></a>Konfigurieren des Host-Generators mit UseConfiguration

Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten

Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern

Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>Verarbeiten der Konfiguration während des App-Starts

Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar. Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).

## <a name="command-line-configuration-provider"></a>Befehlszeilen-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.

Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.

`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird. Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).

`CreateDefaultBuilder` lädt außerdem:

* Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.
* Umgebungsvariablen.

`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu. Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.

`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde. Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.

Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen. Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**Beispiel**

Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.

1. Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.
1. Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.
1. Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.

### <a name="arguments"></a>Argumente

Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt. Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).

| Schlüsselpräfix               | Beispiel                                                |
| ------------------------ | ------------------------------------------------------ |
| Ohne Präfix                | `CommandLineKey1=value1`                               |
| Zwei Gedankenstriche (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Schrägstrich (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.

Beispielbefehle:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>Switchmappings

Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen. Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.

Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft. Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben. Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.

Regeln für Schlüssel von Switchmappingwörterbüchern:

* Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.
* Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.

Erstellen Sie ein Switchmappingwörterbuch. Im folgenden Beispiel werden zwei Switchmappings erstellt:

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden. Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden. Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.

Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.

| Schlüssel       | Wert             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.

| Key               | Wert    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>Umgebungsvariablen-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.

Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können. Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird. Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).

`CreateDefaultBuilder` lädt außerdem:

* App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.
* Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.
* Befehlszeilenargumenten

Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde. Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.

Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.

**Beispiel**

Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.

1. Führen Sie die Beispiel-App aus. Öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält. Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.

Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen. Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.

Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>Präfixe

Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben. Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.

Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt. Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).

**Präfixe für Verbindungszeichenfolgen**

Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden. Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.

| Präfix für Verbindungszeichenfolgen | Anbieter |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:

* Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.
* Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).

| Umgebungsvariablenschlüssel | Konvertierter Konfigurationsschlüssel | Anbieterkonfigurationseintrag                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:<br>Wert: `System.Data.SqlClient`  |

**Beispiel**

Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:

* Name: `CUSTOMCONNSTR_ReleaseDB`
* Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>Dateikonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem. Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:

* [INI-Konfigurationsanbieter](#ini-configuration-provider)
* [JSON-Konfigurationsanbieter](#json-configuration-provider)
* [XML-Konfigurationsanbieter](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.

Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

Ein allgemeines Beispiel einer INI-Konfigurationsdatei:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.

Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren. Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:

* *appsettings.json*: Diese Datei wird zuerst gelesen. Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.
* *appsettings.{Environment}.json*: Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.

Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).

`CreateDefaultBuilder` lädt außerdem:

* Umgebungsvariablen.
* [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.
* Befehlszeilenargumenten

Der JSON-Konfigurationsanbieter wird zuerst eingerichtet. Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**Beispiel**

Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:

* Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*. Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. Führen Sie die Beispiel-App aus. Öffnen Sie einen Browser für die App unter `http://localhost:5000`.
1. Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App. Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.
1. Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:
   1. Öffnen Sie die Datei *Properties/launchSettings.json*.
   1. Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.
   1. Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.
1. Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft. Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.

### <a name="xml-configuration-provider"></a>XML-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.

Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Überladungen geben Folgendes an:

* Ob die Datei optional ist
* Ob die Konfiguration bei Dateiänderungen neu geladen wird
* Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird

Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert. Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section0:key0
* section0:key1
* section1:key0
* section1:key1

Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

Mit Attributen können Werte bereitgestellt werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>Schlüssel-pro-Datei-Konfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare. Der Schlüssel ist der Dateiname. Der Wert enthält den Inhalt der Datei. Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.

Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf. Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.

Überladungen geben Folgendes an:

* Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert
* Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis

Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet. Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>Speicherkonfigurationsanbieter

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.

Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.

Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.

Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.

Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Sammlungstyp ist. Eine Überladung akzeptiert einen Standardwert.

Im Beispiel unten geschieht Folgendes:

* Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert. Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.
* Der Typ für den Wert wird als `int` festgelegt.
* Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren und Exists

Betrachten Sie für die folgenden Beispiele die JSON-Datei unten. Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.

Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:

```csharp
var configSection = _config.GetSection("section1");
```

Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.

Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection` gibt nie `null` zurück. Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.

Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt. Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.

### <a name="getchildren"></a>GetChildren

Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>Vorhanden

Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.

## <a name="bind-to-an-object-graph"></a>Binden an ein Objektdiagramm

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden. Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.

Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden. Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück. `Get<T>` ist praktischer als `Bind`. Der folgende Code zeigt, wie `Get<T>` in Bezug auf das vorherige Beispiel verwendet wird:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln. Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.

> [!NOTE]
> Das Binden ist standardmäßig möglich. Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.

**In-Memory-Arrayverarbeitung**

Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.

| Key             | Wert  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

Das Array überspringt einen Wert für Index &num;3. Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.

In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

Die Konfigurationsdaten werden an das Objekt gebunden:

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann ebenfalls verwendet werden, was zu kompakteren Code führt:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.

| `ArrayExample.Entries`-Index | `ArrayExample.Entries`-Wert |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`. Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet. Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.

Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt. Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

In `ConfigureAppConfiguration`:

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.

| Schlüssel             | Wert  |
| :-------------: | :----: |
| array:entries:3 | value3 |

Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.

| `ArrayExample.Entries`-Index | `ArrayExample.Entries`-Wert |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON-Arrayverarbeitung**

Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt. In der folgenden Konfigurationsdatei ist `subsection` ein Array:

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:

| Key                     | Wert  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`. Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.

| `JsonArrayExample.Subsection`-Index | `JsonArrayExample.Subsection`-Wert |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>Benutzerdefinierter Konfigurationsanbieter

Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.

Der Anbieter weist die folgenden Merkmale auf:

* Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet. Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.
* Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration. Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.
* Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.

Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>. Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>Zugreifen auf die Konfiguration während des Starts

Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen. Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a>Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht

Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.

Auf einer Razor Pages-Seite:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

In einer MVC-Ansicht:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>Hinzufügen von Konfigurationen aus einer externen Assembly

Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/options>

::: moniker-end
