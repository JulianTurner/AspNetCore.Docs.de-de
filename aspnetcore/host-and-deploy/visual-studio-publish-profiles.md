---
title: Visual Studio-Veröffentlichungsprofile (PUBXML) für die Bereitstellung von ASP.NET Core-Apps
author: rick-anderson
description: Informationen zum Erstellen von Veröffentlichungsprofilen in Visual Studio und zu deren Verwendung zum Verwalten von Bereitstellungen von ASP.NET Core-Apps an verschiedene Ziele.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: e86a648e2707e808452e3bc92d991058c8fde480
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754644"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Visual Studio-Veröffentlichungsprofile (PUBXML) für die Bereitstellung von ASP.NET Core-Apps

Von [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieser Artikel konzertiert sich auf die Verwendung von Visual Studio 2019 oder höher zum Erstellen und Verwenden von Veröffentlichungsprofilen. Die Veröffentlichungsprofile, die mit Visual Studio erstellt werden, können mit MSBuild und Visual Studio verwendet werden. Anweisungen zum Veröffentlichen in Azure finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.

Der Befehl `dotnet new mvc` generiert eine Projektdatei, die das folgende [\<Project>-Element](/visualstudio/msbuild/project-element-msbuild) auf Stammebene enthält:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

Das `Sdk`-Attribut des vorhergehenden `<Project>`-Elements importiert die [Eigenschaften](/visualstudio/msbuild/msbuild-properties) und [Ziele](/visualstudio/msbuild/msbuild-targets) von MSBuild aus *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.reps* bzw. *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk*. Der Standardspeicherort für `$(MSBuildSDKsPath)` (mit Visual Studio 2019 Enterprise) ist der Ordner *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` ([Web-SDK](xref:razor-pages/web-sdk)) hängt von anderen SDKs ab, einschließlich `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) und `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)). Die MSBuild-Eigenschaften und -Ziele, die jedem abhängigen SDK zugeordnet sind, werden importiert. Veröffentlichungsziele importieren basierend auf der verwendeten Veröffentlichungsmethode die entsprechende Gruppe von Zielen.

Wenn in MSBuild oder Visual Studio ein Projekt geladen wird, werden die folgenden Aktionen auf hoher Ebene ausgeführt:

* Erstellen des Projekts
* Berechnen der zu veröffentlichenden Dateien
* Veröffentlichen der Dateien auf dem Ziel

## <a name="compute-project-items"></a>Berechnen der Projektelemente

Wenn das Projekt geladen ist, werden die [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) (Dateien) berechnet. Der Elementtyp bestimmt, wie die Datei verarbeitet wird. Standardmäßig sind *CS*-Dateien in der `Compile`-Elementliste enthalten. Dateien in der `Compile`-Elementliste werden kompiliert.

Die `Content`-Elementliste enthält Dateien, die zusätzlich zu den Buildausgaben veröffentlicht werden. Standardmäßig sind Dateien, die mit den Mustern `wwwroot\**`, `**\*.config` und `**\*.json` übereinstimmen, in der Elementliste `Content` enthalten. Das `wwwroot\**` [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns) stimmt z. B. mit allen Dateien im *wwwroot*-Ordner und in dessen Unterordnern überein.

::: moniker range=">= aspnetcore-3.0"

Das [Web-SDK](xref:razor-pages/web-sdk) importiert das [Razor SDK](xref:razor-pages/sdk). Demzufolge sind Dateien, die mit den Mustern `**\*.cshtml` und `**\*.razor` übereinstimmen, in der Elementliste `Content` enthalten.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Das [Web-SDK](xref:razor-pages/web-sdk) importiert das [Razor SDK](xref:razor-pages/sdk). Demzufolge sind Dateien, die mit dem Muster `**\*.cshtml` übereinstimmen, in der Elementliste `Content` enthalten.

::: moniker-end

Um eine Datei explizit der Veröffentlichungsliste hinzuzufügen, fügen Sie die Datei, wie im Abschnitt [Includedateien](#include-files) gezeigt, direkt der *CSPROJ*-Datei hinzu.

Wenn Sie auf die Schaltfläche **Veröffentlichen** in Visual Studio klicken oder aus der Befehlszeile veröffentlichen:

* Die Eigenschaften und Elemente werden berechnet (die Dateien, die für den Build benötigt werden).
* **Nur Visual Studio**: NuGet-Pakete werden wiederhergestellt. (Die Wiederherstellung muss explizit vom Benutzer auf der CLI durchgeführt werden.)
* Das Projekt wird erstellt.
* Die zu veröffentlichenden Elemente werden berechnet (die Dateien, die für die Veröffentlichung benötigt werden).
* Das Projekt wird veröffentlicht (die berechneten Dateien werden in das Veröffentlichungsziel kopiert).

Wenn ein ASP.NET Core-Projekt auf `Microsoft.NET.Sdk.Web` in der Projektdatei verweist, wird eine *app_offline.htm*-Datei im Stammverzeichnis des Verzeichnisses der Web-App abgelegt. Wenn die Datei vorhanden ist, fährt das ASP.NET Core Module die App ordnungsgemäß herunter und verarbeitet die Datei *app_offline.htm* während der Bereitstellung. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Grundlegendes zur Veröffentlichung über die Befehlszeile

Die Veröffentlichung über die Befehlszeile funktioniert auf allen von .NET Core unterstützten Plattformen und setzt Visual Studio nicht voraus. In den folgenden Beispielen wird der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) der .NET Core-CLI im Projektverzeichnis ausgeführt (das die Datei *CSPROJ* enthält). Wenn der Projektordner nicht das aktuelle Arbeitsverzeichnis ist, übergeben Sie den Projektdateipfad explizit. Zum Beispiel:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Führen Sie die folgenden Befehle zum Erstellen und Veröffentlichen eine Web-App aus:

```dotnetcli
dotnet new mvc
dotnet publish
```

Der Befehl `dotnet publish` erzeugt eine Abwandlung der folgenden Ausgabe:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

Das Standardformat des Veröffentlichungsordners ist *bin\Debug\\{ZIELFRAMEWORKMONIKER}\publish\\* . Dies ist dann z. B *bin\Debug\netcoreapp2.2\publish\\* .

Der folgende Befehl gibt einen `Release`-Build und das Veröffentlichungsverzeichnis an:

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Der Befehl `dotnet publish` ruft MSBuild auf, welches das `Publish`-Ziel aufruft. Alle Parameter, die an `dotnet publish` übergeben werden, werden an MSBuild übergeben. Die Parameter `-c` und `-o` verweisen auf die MSBuild-Eigenschaften `Configuration` bzw. `OutputPath`.

MSBuild-Eigenschaften können mithilfe eines der folgenden Formate übergeben werden:

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Der folgende Befehl veröffentlicht z. B. einen `Release`-Build in einer Netzwerkfreigabe. Die Netzwerkfreigabe wird durch führende Schrägstriche ( *//r8/* ) angegeben und funktioniert auf allen Plattformen, die von .NET Core unterstützt werden.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Versichern Sie sich, dass die veröffentlichte App für die Bereitstellung nicht ausgeführt wird. Die Dateien im Ordner *publish* sind gesperrt, wenn die App ausgeführt wird. Die Bereitstellung kann nicht erfolgen, da die gesperrten Dateien nicht kopiert werden können.

## <a name="publish-profiles"></a>Veröffentlichungsprofile

In diesem Abschnitt wird Visual Studio 2019 oder höher verwendet, um ein Veröffentlichungsprofil zu erstellen. Nachdem das Profil erstellt ist, ist Veröffentlichung aus Visual Studio oder über die Befehlszeile möglich. Veröffentlichungsprofile können den Veröffentlichungsprozess vereinfachen, und eine beliebige Anzahl von Profilen kann vorhanden sein.

Erstellen Sie ein Veröffentlichungsprofil in Visual Studio durch Auswahl eines der folgenden Pfade:

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
* Wählen Sie **{PROJEKTNAME} veröffentlichen** im Menü **Build** aus.

Die Registerkarte **Veröffentlichen** der Seite für App-Funktionen wird angezeigt. Wenn das Projekt kein Veröffentlichungsprofil enthält, wird die Seite **Veröffentlichungsziel auswählen** angezeigt. Sie können aus den folgenden Veröffentlichungszielen wählen:

* Azure App Service
* Azure App Service für Linux
* Azure Virtual Machines
* Ordner
* IIS, FTP, Web Deploy (für einen beliebigen Webserver)
* Profil importieren

Unter [Übersicht über die Bereitstellung in Visual Studio](/visualstudio/ide/not-in-toc/web-publish-options) erfahren Sie, wie Sie das ideale Veröffentlichungsziel bestimmen.

Wenn **Ordner** als Veröffentlichungsziel ausgewählt ist, geben Sie einen Ordnerpfad ein, um die veröffentlichten Objekte zu speichern. Der Standardordnerpfad ist *bin\\{PROJEKTKONFIGURATION}\\{ZIELFRAMEWORKMONIKER}\publish\\* . Dies ist dann z. B. *bin\Release\netcoreapp2.2\publish\\* . Klicken Sie auf die Schaltfläche **Profil erstellen**, um den Vorgang abzuschließen.

Sobald ein Veröffentlichungsprofil erstellt wurde, ändert sich der Inhalt der Registerkarte **Veröffentlichen**. Das neu erstellte Profil wird in einer Dropdownliste angezeigt. Klicken Sie unter der Dropdownliste auf **Neues Profil erstellen**, um ein weiteres neues Profil zu erstellen.

Das Visual Studio-Veröffentlichungstool erstellt die MSBuild-Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml*, in der das Veröffentlichungsprofil beschrieben wird. Die *PUBXML*-Datei:

* enthält Veröffentlichungskonfigurationseinstellungen und wird vom Veröffentlichungsprozess verwendet.
* kann geändert werden, um den Build- und Veröffentlichungsprozess anzupassen.

Beim Veröffentlichen in einem Azure-Ziel enthält die *PUBXML*-Datei Ihre Azure-Abonnement-ID. Bei diesem Zieltyp wird vom Hinzufügen der Datei zur Quellkontrolle abgeraten. Beim Veröffentlichen auf einem Nicht-Azure-Ziel kann die *PUBXML*-Datei eingecheckt werden.

Vertrauliche Informationen (z.B. das Kennwort für die Veröffentlichung) werden pro Benutzer/Computer verschlüsselt. Sie werden in der Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml.user* gespeichert. Da diese Datei vertrauliche Informationen enthalten kann, sollte sie nicht in die Quellverwaltung eingecheckt werden.

Mehr Informationen zum Veröffentlichen einer ASP.NET Core-Web-App finden Sie unter <xref:host-and-deploy/index>. Die MSBuild-Tasks und -Ziele, die zum Veröffentlichen einer ASP.NET Core-Web-App erforderlich sind, sind als Open-Source-Code im Repository [dotnet/websdk](https://github.com/dotnet/websdk) verfügbar.

Die folgenden Befehle können Ordner, MSDeploy und [Kudu](https://github.com/projectkudu/kudu/wiki)-Veröffentlichungsprofile verwenden. Da MSDeploy nicht plattformübergreifend unterstützt wird, werden die folgenden MSDeploy-Optionen nur unter Windows unterstützt:

**Ordner (funktioniert plattformübergreifend):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**MSDeploy-Paket:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

In den vorherigen Beispielen:

* `dotnet publish` und `dotnet build` unterstützen Kudu-APIs zur Veröffentlichung in Azure von jeder Plattform aus. Die Visual Studio-Veröffentlichung unterstützt die Kudu-APIs, wird für die plattformübergreifende Veröffentlichung in Azure aber vom WebSDK unterstützt.
* Übergeben Sie `DeployOnBuild` nicht an den `dotnet publish`-Befehl.

Weitere Informationen finden Sie unter [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Fügen Sie dem Projektordner *Properties/PublishProfiles* ein Veröffentlichungsprofil mit folgendem Inhalt hinzu:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Beispiel für Ordnerveröffentlichung

Verwenden Sie einen der folgenden Befehle, wenn Sie mit einem Profil namens *FolderProfile* veröffentlichen:

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Der .NET Core-CLI-Befehl [dotnet build](/dotnet/core/tools/dotnet-build) ruft `msbuild` auf, um den Build- und Veröffentlichungsprozess auszuführen. Die Befehle `dotnet build` und `msbuild` sind bei der Übergabe eines Ordnerprofils gleichwertig. Wenn Sie `msbuild` direkt in Windows aufrufen, wird die .NET Framework-Version von MSBuild verwendet. Wenn Sie `dotnet build` in einem Profil ohne Ordner aufrufen:

* wird das `msbuild`-Objekt aufgerufen, das MSDeploy verwendet.
* führt dies zu einem Fehler (auch unter Windows). Rufen Sie `msbuild` direkt auf, um eine Veröffentlichung mit einem Profil ohne Ordner durchzuführen.

Der folgende Ordner „Veröffentlichungsprofil“ wurde mit Visual Studio erstellt und veröffentlicht in eine Netzwerkfreigabe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

Im vorherigen Beispiel:

* Die Eigenschaft `<ExcludeApp_Data>` ist nur vorhanden, um die XML-Schemaanforderung zu erfüllen. Die Eigenschaft `<ExcludeApp_Data>` wirkt sich nicht auf den Veröffentlichungsprozess aus, auch wenn es den Ordner *App_Data* am Projektstamm gibt. Der Ordner *App_Data* wird genauso wie in ASP.NET 4.x-Projekten behandelt.
* Die `<LastUsedBuildConfiguration>`-Eigenschaft ist auf `Release` festgelegt. Bei der Veröffentlichung von Visual Studio wird der `<LastUsedBuildConfiguration>`-Wert mithilfe des Werts festgelegt, der beim Starten des Veröffentlichungsprozesses vorliegt. `<LastUsedBuildConfiguration>` ist ein Sonderfall und sollte nicht in einer importierten MSBuild-Datei überschrieben werden. Diese Eigenschaft kann aber über die Befehlszeile mit einer der folgenden Vorgehensweisen überschrieben werden.
  * Mit der .NET Core-CLI:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * Verwenden von MSBuild:

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Weitere Informationen finden Sie unter [MSBuild: how to set the configuration property (MSBuild: Festlegen der Konfigurationseigenschaft)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Veröffentlichen auf einen MSDeploy-Endpunkt von der Befehlszeile

Im folgenden Beispiel wird von Visual Studio eine ASP.NET Core-Web-App namens *AzureWebApp* erstellt. Ein Azure Apps-Veröffentlichungsprofil wird mit Visual Studio hinzugefügt. Weitere Informationen zum Erstellen eines Profils finden Sie unter im Abschnitt [Veröffentlichungsprofile](#publish-profiles).

Um eine App mit einem Veröffentlichungsprofil bereitzustellen, führen Sie den Befehl `msbuild` an einer **Developer-Eingabeaufforderung** von Visual Studio aus. Die Eingabeaufforderung finden Sie im Ordner *Visual Studio* im **Start**-Menü auf der Windows-Taskleiste. Für einfacheren Zugriff können Sie die Eingabeaufforderung zum Menü **Tools** in Visual Studio hinzufügen. Weitere Informationen finden Sie unter [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild hat die folgende Befehlssyntax:

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`: Der Pfad zur Projektdatei der App.
* `{PROFILE}`: Der Name des Veröffentlichungsprofils.
* `{USERNAME}`: Der MSDeploy-Benutzername. `{USERNAME}` befindet sich im Veröffentlichungsprofil.
* `{PASSWORD}`: Das MSDeploy-Kennwort. `{PASSWORD}` befindet sich in der Datei *{PROFILE}.PublishSettings*. Laden Sie die Datei *.PublishSettings* von einem der folgenden Speicherorte herunter:
  * **Projektmappen-Explorer:** Wählen Sie **Ansicht** > **Cloud-Explorer** aus. Stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her. Öffnen Sie **App Services**. Klicken Sie mit der rechten Maustaste auf die App. Wählen Sie **Veröffentlichungsprofil herunterladen** aus.
  * Azure-Portal: Wählen Sie **Veröffentlichungsprofil abrufen** im Bereich **Übersicht** der Web-App aus.

Im folgenden Beispiel wird ein Veröffentlichungsprofil namens *AzureWebApp – Web Deploy* verwendet:

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Ein Veröffentlichungsprofil kann auch mit dem .NET Core-CLI-Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) über eine Windows-Befehlsshell verwendet werden:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Der Befehl `dotnet msbuild` ist plattformübergreifend verfügbar und kann ASP.NET Core-Apps unter macOS und Linux kompilieren. Allerdings kann MSBuild unter macOS und Linux keine Apps in Azure oder an einem anderen MSDeploy-Endpunkt bereitstellen.

## <a name="set-the-environment"></a>Festlegen der Umgebung

Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein, um die [Umgebung](xref:fundamentals/environments) der App festzulegen:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Wenn Sie *web.config*-Transformationen benötigen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Dateien ausschließen

Wenn Sie ASP.NET Core-Web-Apps veröffentlichen, sind die folgenden Ressourcen enthalten:

* Build-Artefakte
* Ordner und Dateien, die den folgenden Globmustern entsprechen:
  * `**\*.config` (Beispiel: *web.config*)
  * `**\*.json` (Beispiel: *appsettings.json*)
  * `wwwroot\**`

MSBuild unterstützt [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns). Das folgende `<Content>`-Element unterdrückt beispielsweise das Kopieren von Textdateien (*TXT*) aus dem Ordner *wwwroot/content* und allen seinen Unterordnern:

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Das vorstehende Markup kann einem Veröffentlichungsprofil oder der *CSPROJ*-Datei hinzugefügt werden. Wenn es zu der *CSPROJ*-Datei hinzugefügt wird, wird die Regel zu allen Veröffentlichungsprofilen im Projekt hinzugefügt.

Das folgende `<MsDeploySkipRules>`-Element schließt alle Dateien aus dem Ordner *wwwroot\content* aus:

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

`<MsDeploySkipRules>` löscht die *Skip*-Ziele von der Bereitstellungsseite nicht. `<Content>`-Zieldateien und -ordner werden von der Bereitstellungsseite gelöscht. Nehmen wir beispielsweise an, dass eine bereitgestellte Web-App folgende Dateien enthält:

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Wenn folgende `<MsDeploySkipRules>`-Elemente hinzugefügt werden, würden diese Dateien nicht von der Bereitstellungsseite gelöscht werden.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Die vorangehenden `<MsDeploySkipRules>`-Elemente verhindern, dass *übersprungene* Dateien bereitgestellt werden. Diese Dateien werden nach ihrer Bereitstellung nicht gelöscht.

Das folgende `<Content>`-Element löscht die Zieldateien auf der Bereitstellungsseite:

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Die Bereitstellung über die Befehlszeile mit dem vorangehenden `<Content>`-Element gibt eine Abwandlung der folgenden Ausgabe aus:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Includedateien

Die folgenden Abschnitte beschreiben verschiedene Ansätze für das Einbeziehen von Dateien beim Veröffentlichen. Im Abschnitt [Allgemeines Einbeziehen von Dateien](#general-file-inclusion) wird das Element `DotNetPublishFiles` verwendet, das von einer Veröffentlichungszieledatei im [Web-SDK](xref:razor-pages/web-sdk) bereitgestellt wird. Im Abschnitt [Selektives Einbeziehen von Dateien](#selective-file-inclusion) wird das Element `ResolvedFileToPublish` verwendet, das von einer Veröffentlichungszieledatei im [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props) bereitgestellt wird. Weil das Web-SDK vom .NET Core SDK abhängig ist, können beide Elemente in einem ASP.NET Core-Projekt verwendet werden.

### <a name="general-file-inclusion"></a>Allgemeines Einbeziehen von Dateien

Das Element `<ItemGroup>` im folgenden Beispiel veranschaulicht das Kopieren eines Ordners, der sich außerhalb des Projektverzeichnisses befindet, in einen Ordner der veröffentlichten Website. Alle Dateien, die dem `<ItemGroup>`-Element im folgenden Markup-Ausschnitt hinzugefügt werden, werden standardmäßig einbezogen.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Das obenstehende Markup:

* Kann der *CSPROJ*-Datei oder dem Veröffentlichungsprofil hinzugefügt werden. Wenn es der *CSPROJ*-Datei hinzugefügt wird, wird es in jedes Veröffentlichungsprofil im Projekt eingeschlossen.
* Deklariert ein `_CustomFiles`-Element, um Dateien zu speichern, die mit dem Globmuster des `Include`-Attributs übereinstimmen. Der Ordner *images*, auf den im Muster verwiesen wird, befindet sich außerhalb des Projektverzeichnisses. Die [reservierte Eigenschaft](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)`$(MSBuildProjectDirectory)` löst den absoluten Pfad der Projektdatei auf.
* Stellt eine Liste der zu verarbeitenden `DotNetPublishFiles`-Elemente bereit. Das `<DestinationRelativePath>`-Element des Elements ist standardmäßig leer. Der Standardwert wird im Markup überschrieben und verwendet [bekannte Elementmetadaten](/visualstudio/msbuild/msbuild-well-known-item-metadata) wie z. B. `%(RecursiveDir)`. Der innere Text stellt den Ordner *wwwroot/images* der veröffentlichten Website dar.

### <a name="selective-file-inclusion"></a>Selektives Einbeziehen von Dateien

Der markierte Markup-Code im folgenden Beispiel veranschaulicht Folgendes:

* Das Kopieren einer Datei, die sich außerhalb eines Projekts befindet, in den *wwwroot*-Ordner der veröffentlichten Website. Der Dateiname *ReadMe2.md* wird beibehalten.
* Ausschließen des Ordners *wwwroot\Content*.
* Ausschließen von *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

Im vorherigen Beispiel wird das Element `ResolvedFileToPublish` verwendet, dessen Standardverhalten darin besteht, die im `Include`-Attribut bereitgestellten Dateien in die veröffentlichte Website zu kopieren. Sie können das Standardverhalten überschreiben, indem Sie ein untergeordnetes `<CopyToPublishDirectory>`-Element mit dem inneren Text `Never` oder `PreserveNewest` einbeziehen. Zum Beispiel:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Weitere Bereitstellungsbeispiele finden Sie in der [README-Datei des Web SDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Führen Sie ein Ziel vor oder nach der Veröffentlichung aus.

Die integrierten Ziele `BeforePublish` und `AfterPublish` führen ein Ziel vor oder nach dem Veröffentlichungsziel aus. Fügen Sie die folgenden Elemente dem Veröffentlichungsprofil hinzu, um Konsolenmeldungen vor und nach der Veröffentlichung zu protokollieren:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Veröffentlichen auf einem Server unter Verwendung eines nicht vertrauenswürdigen Zertifikats

Fügen Sie die Eigenschaft `<AllowUntrustedCertificate>` mit dem Wert `True` auf dem Veröffentlichungsprofil hinzu:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Der Kudu-Dienst

Verwenden Sie zum Anzeigen der Dateien in einer Web-App-Bereitstellung von Azure App Service den [Kudu-Dienst](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Fügen Sie das Token `scm` an den Namen Ihrer Web-App an. Zum Beispiel:

| URL                                    | Ergebnis       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Webanwendung      |
| `http://mysite.scm.azurewebsites.net/` | Der Kudu-Dienst |

Wählen Sie das Menüelement [Debugging-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console), aus, um Dateien anzuzeigen, zu bearbeiten, zu löschen oder hinzuzufügen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) vereinfacht die Bereitstellung von Web-Apps und Websites auf IIS-Servern.
* [GitHub-Repository für Web SDK](https://github.com/dotnet/websdk/issues): Probleme melden und Features für die Bereitstellung anfordern.
* [Veröffentlichen einer ASP.NET-Web-App auf einer Azure-VM aus Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
