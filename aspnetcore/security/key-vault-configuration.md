---
title: Azure Key Vault Konfigurations Anbieters in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit dem Azure Key Vault-Konfigurations Anbieter eine App mithilfe von Name-Wert-Paaren konfigurieren, die zur Laufzeit geladen werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 7f5cd3de38f1e45d9b188c513a0e62ca658b2992
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035904"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Azure Key Vault Konfigurations Anbieters in ASP.net Core

Von [Andrew Stanton-Nurse](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

In diesem Dokument wird erläutert, wie Sie den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden. Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden. Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:

* Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.
* Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakete

Fügen Sie einen Paket Verweis auf die [Azure.Extensions.AspNetCore.Config-urung hinzu. Geheimes](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets/) Paket.

## <a name="sample-app"></a>Beispiel-App

Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:

* `Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse. Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.
* `Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind. Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich. Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden. Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .

Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung

Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest. Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden die geheimen Schlüssel aus dem lokalen Benutzer Geheimnis Speicher geladen.

Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app. Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Geheimnisse werden als Name-Wert-Paare erstellt. Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.

Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` . Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault

Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden. Weitere Informationen finden Sie im Thema.

1. Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:

   * Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.
   * Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .
   * Wählen Sie im Menü in der oberen rechten Ecke des Azure-Portal die Schaltfläche **Cloud Shell** aus.

   Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).

1. Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.

1. Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.

   Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt. Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen. Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig. Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.

   Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen. Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden. Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.

Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**. Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden. Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.

Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .

1. Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*). Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).
1. Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers. Das Markieren des Schlüssels als exportierbar ist optional. Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.
1. Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).
1. Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).
1. Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:
   1. Wählen Sie die app in Azure AD aus.
   1. Navigieren Sie zu **Zertifikate & Geheimnissen**.
   1. Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält. Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.
1. Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der APP- *appsettings.json* Datei.
1. Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .
1. Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.
1. Klicken Sie auf **Zugriffsrichtlinien**.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.
1. Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.
1. Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus. Wählen Sie die Schaltfläche **Auswählen** aus.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.
1. Stellen Sie die App bereit.

Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:

* Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .
* Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode. Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Das X. 509-Zertifikat wird vom Betriebssystem verwaltet. Die App Ruft **addazurekeyvault** mit Werten auf, die von der Datei bereitgestellt werden *appsettings.json* :

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

Beispielwerte:

* Key Vault-Name: `contosovault`
* Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* Zertifikat Fingerabdruck: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen. In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.

In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .

Geben Sie den Tresor Namen in die Datei der APP ein *appsettings.json* . Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können. Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei gespeichert ist *appsettings.json* .

Stellen Sie die Beispiel-App für Azure App Service bereit.

Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird. Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden. Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.

Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.

Die Beispiel-App:

* Erstellt eine Instanz der- `DefaultAzureCredential` Klasse. die Anmelde Informationen versuchen, ein Zugriffs Token aus der Umgebung für Azure-Ressourcen abzurufen.
* Ein neues [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) wird mit der- `DefaultAzureCredential` Instanz erstellt.
* Die- `Azure.Security.KeyVault.Secrets.Secrets` Instanz wird mit einer Standard Implementierung von verwendet `Azure.Extensions.Aspnetcore.Configuration.Secrets` , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

Beispiel Wert für Key Vault-Name: `contosovault`

*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden. In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.

Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat. Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.

Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="configuration-options"></a>Konfigurationsoptionen

Addazurekeyvault kann azurekeyvaultconfigurationoptions akzeptieren:

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| Eigenschaft         | Beschreibung |
| ---------------- | ----------- |
| `Manager`        | `Azure.Extensions.Aspnetcore.Configuration.Secrets` zum Steuern des geheimen Schlüssels verwendete Instanz. |
| `ReloadInterval` | `Timespan` , um zwischen versuchen zu warten, den Schlüssel Tresor auf Änderungen abzufragen. Der Standardwert ist `null` (die Konfiguration wird nicht neu geladen). |

## <a name="use-a-key-name-prefix"></a>Schlüsselnamen Präfix verwenden

Addazurekeyvault stellt eine Überladung bereit, die eine Implementierung von akzeptiert `Azure.Extensions.Aspnetcore.Configuration.Secrets` , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden. Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen. So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.

> [!WARNING]
> Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren. Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.

Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig). Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar. Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` . Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.

"Addazurekeyvault" wird mit einem benutzerdefinierten aufgerufen `Azure.Extensions.Aspnetcore.Configuration.Secrets` :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Die `Azure.Extensions.Aspnetcore.Configuration.Secrets` Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:

* `Load` lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt. Andere Geheimnisse werden nicht geladen.
* `GetKey`:
  * Entfernt das Präfix aus dem Namen des geheimen Schlüssels.
  * Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist. Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen. Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben. Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.

Wenn dieser Ansatz implementiert ist:

1. Die in der Projektdatei der APP angegebene Version der app. Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen. Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .

1. Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt. Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.

1. Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .

> [!NOTE]
> Sie können auch eine eigene <xref:Azure.Security.KeyVault.Secrets.SecretClient> Implementierung für "addazurekeyvault" bereitstellen. Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.

Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ). Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden. Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte). Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .

Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken* reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:

| Key | Wert |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Geheimnisse erneut laden

Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird. Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Deaktivierte und abgelaufene Geheimnisse

Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Azure.RequestFailedException> . Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.

## <a name="troubleshoot"></a>Problembehandlung

Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben. Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:

* Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.
* Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.
* Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.
* Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.
* Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.
* Die APP verfügt über den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ) oder Azure AD directoryid ( `AzureADDirectoryId` ).
* Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.
* Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault-Dokumentation](/azure/key-vault/)
* [Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Keyvaultclient-Klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App](/azure/key-vault/quick-create-net)
* [Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden. Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden. Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:

* Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.
* Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="packages"></a>Pakete

Fügen Sie einen Paket Verweis auf die [Microsoft.Extensions.Config-urung hinzu. Azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) -Paket.

## <a name="sample-app"></a>Beispiel-App

Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:

* `Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse. Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.
* `Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind. Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich. Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden. Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .

Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .

## <a name="secret-storage-in-the-development-environment"></a>Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung

Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest. Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden die geheimen Schlüssel aus dem lokalen Benutzer Geheimnis Speicher geladen.

Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app. Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Geheimnisse werden als Name-Wert-Paare erstellt. Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.

Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` . Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault

Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden. Weitere Informationen finden Sie im Thema.

1. Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:

   * Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**. Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.
   * Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .
   * Wählen Sie im Menü in der oberen rechten Ecke des Azure-Portal die Schaltfläche **Cloud Shell** aus.

   Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).

1. Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.

1. Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.

   Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt. Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen. Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig. Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.

   Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen. Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden. Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.

Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**. Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden. Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.

Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .

1. Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*). Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).
1. Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers. Das Markieren des Schlüssels als exportierbar ist optional. Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.
1. Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).
1. Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).
1. Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:
   1. Wählen Sie die app in Azure AD aus.
   1. Navigieren Sie zu **Zertifikate & Geheimnissen**.
   1. Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält. Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.
1. Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der APP- *appsettings.json* Datei.
1. Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .
1. Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.
1. Klicken Sie auf **Zugriffsrichtlinien**.
1. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.
1. Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.
1. Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus. Wählen Sie die Schaltfläche **Auswählen** aus.
1. Klicken Sie auf **OK**.
1. Wählen Sie **Speichern** aus.
1. Stellen Sie die App bereit.

Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:

* Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .
* Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode. Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Das X. 509-Zertifikat wird vom Betriebssystem verwaltet. Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der Datei bereitgestellt werden *appsettings.json* :

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Beispielwerte:

* Key Vault-Name: `contosovault`
* Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* Zertifikat Fingerabdruck: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen. In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.

## <a name="use-managed-identities-for-azure-resources"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen

**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.

In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .

Geben Sie den Tresor Namen in die Datei der APP ein *appsettings.json* . Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können. Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei gespeichert ist *appsettings.json* .

Stellen Sie die Beispiel-App für Azure App Service bereit.

Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird. Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden. Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.

Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.

Die Beispiel-App:

* Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge. Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.
* Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.
* Die- <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer Standard Implementierung von verwendet <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Beispiel Wert für Key Vault-Name: `contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt. In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden. In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.

Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat. Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.

Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).

## <a name="use-a-key-name-prefix"></a>Schlüsselnamen Präfix verwenden

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> stellt eine Überladung bereit, die eine Implementierung von akzeptiert <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden. Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen. So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.

> [!WARNING]
> Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren. Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.

Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig). Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar. Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` . Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wird mit einem benutzerdefinierten aufgerufen <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:

* `Load` lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt. Andere Geheimnisse werden nicht geladen.
* `GetKey`:
  * Entfernt das Präfix aus dem Namen des geheimen Schlüssels.
  * Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist. Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen. Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben. Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.

Wenn dieser Ansatz implementiert ist:

1. Die in der Projektdatei der APP angegebene Version der app. Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen. Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .

1. Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt. Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.

1. Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .

> [!NOTE]
> Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für bereitstellen <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> . Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.

## <a name="bind-an-array-to-a-class"></a>Binden eines Arrays an eine Klasse

Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.

Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ). Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).

Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden. Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte). Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .

Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken* reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:

| Key | Wert |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Geheimnisse erneut laden

Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird. Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Deaktivierte und abgelaufene Geheimnisse

Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> . Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.

## <a name="troubleshoot"></a>Problembehandlung

Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben. Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:

* Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.
* Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.
* Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.
* Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.
* Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.
* Die APP hat den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ).
* Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.
* Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Key Vault-Dokumentation](/azure/key-vault/)
* [Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault](/azure/key-vault/key-vault-hsm-protected-keys)
* [Keyvaultclient-Klasse](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App](/azure/key-vault/quick-create-net)
* [Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
