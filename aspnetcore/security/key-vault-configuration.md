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
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792079"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="36968-103">Azure Key Vault Konfigurations Anbieters in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="36968-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="36968-104">Von [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="36968-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36968-105">In diesem Dokument wird erläutert, wie Sie den [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="36968-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="36968-106">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="36968-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="36968-107">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="36968-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="36968-108">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="36968-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="36968-109">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="36968-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="36968-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36968-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="36968-111">Pakete</span><span class="sxs-lookup"><span data-stu-id="36968-111">Packages</span></span>

<span data-ttu-id="36968-112">Fügen Sie Paket Verweise für die folgenden Pakete hinzu:</span><span class="sxs-lookup"><span data-stu-id="36968-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="36968-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="36968-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="36968-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="36968-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="36968-115">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="36968-115">Sample app</span></span>

<span data-ttu-id="36968-116">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="36968-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="36968-117">`Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="36968-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="36968-118">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="36968-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="36968-119">`Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="36968-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="36968-120">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="36968-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="36968-121">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="36968-122">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="36968-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="36968-123">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="36968-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="36968-124">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="36968-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="36968-125">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="36968-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="36968-126">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden die geheimen Schlüssel aus dem lokalen Benutzer Geheimnis Speicher geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="36968-127">Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="36968-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="36968-128">Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="36968-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="36968-129">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="36968-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="36968-130">Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="36968-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="36968-131">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="36968-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="36968-132">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="36968-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="36968-133">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` .</span><span class="sxs-lookup"><span data-stu-id="36968-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="36968-134">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="36968-135">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="36968-136">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="36968-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="36968-137">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="36968-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="36968-138">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="36968-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="36968-139">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="36968-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="36968-140">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="36968-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="36968-141">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="36968-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="36968-142">Wählen Sie im Menü in der oberen rechten Ecke des Azure-Portal die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="36968-143">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="36968-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="36968-144">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="36968-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="36968-145">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="36968-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="36968-146">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="36968-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="36968-147">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="36968-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="36968-148">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="36968-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="36968-149">Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="36968-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="36968-150">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="36968-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="36968-151">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="36968-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="36968-152">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="36968-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="36968-153">Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="36968-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="36968-154">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="36968-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="36968-155">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="36968-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="36968-156">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="36968-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="36968-157">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="36968-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="36968-158">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="36968-159">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="36968-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="36968-160">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="36968-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="36968-161">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="36968-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="36968-162">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="36968-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="36968-163">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="36968-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="36968-164">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="36968-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="36968-165">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="36968-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="36968-166">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="36968-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="36968-167">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="36968-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="36968-168">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="36968-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="36968-169">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="36968-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="36968-170">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="36968-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="36968-171">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="36968-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="36968-172">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="36968-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="36968-173">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der APP- *appsettings.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="36968-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="36968-174">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="36968-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="36968-175">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="36968-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="36968-176">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="36968-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="36968-177">Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="36968-178">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="36968-179">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="36968-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="36968-180">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="36968-181">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="36968-181">Select **OK**.</span></span>
1. <span data-ttu-id="36968-182">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-182">Select **Save**.</span></span>
1. <span data-ttu-id="36968-183">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-183">Deploy the app.</span></span>

<span data-ttu-id="36968-184">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="36968-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="36968-185">Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="36968-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="36968-186">Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="36968-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="36968-187">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="36968-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="36968-188">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="36968-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="36968-189">Die App Ruft **addazurekeyvault** mit Werten auf, die von der Datei bereitgestellt werden *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="36968-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="36968-190">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="36968-190">Example values:</span></span>

* <span data-ttu-id="36968-191">Key Vault-Name: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="36968-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="36968-192">Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="36968-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="36968-193">Zertifikat Fingerabdruck: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="36968-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="36968-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36968-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="36968-195">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="36968-196">In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="36968-197">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="36968-198">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36968-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="36968-199">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="36968-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="36968-200">In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .</span><span class="sxs-lookup"><span data-stu-id="36968-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="36968-201">Geben Sie den Tresor Namen in die Datei der APP ein *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36968-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="36968-202">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="36968-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="36968-203">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei gespeichert ist *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36968-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="36968-204">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="36968-205">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="36968-206">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="36968-207">Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="36968-208">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="36968-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="36968-209">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="36968-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="36968-210">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="36968-210">The sample app:</span></span>

* <span data-ttu-id="36968-211">Erstellt eine Instanz der- `DefaultAzureCredential` Klasse. die Anmelde Informationen versuchen, ein Zugriffs Token aus der Umgebung für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="36968-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="36968-212">Ein neues [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) wird mit der- `DefaultAzureCredential` Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="36968-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="36968-213">Die- `Azure.Security.KeyVault.Secrets.Secrets` Instanz wird mit einer Standard Implementierung von verwendet `Azure.Extensions.AspNetCore.Configuration.Secrets` , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="36968-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="36968-214">Beispiel Wert für Key Vault-Name: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="36968-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="36968-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36968-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="36968-216">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="36968-217">In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="36968-218">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="36968-219">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="36968-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="36968-220">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="36968-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="36968-221">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="36968-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="36968-222">Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="36968-222">Configuration options</span></span>

<span data-ttu-id="36968-223">Addazurekeyvault kann azurekeyvaultconfigurationoptions akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="36968-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="36968-224">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="36968-224">Property</span></span>         | <span data-ttu-id="36968-225">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="36968-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="36968-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` zum Steuern des geheimen Schlüssels verwendete Instanz.</span><span class="sxs-lookup"><span data-stu-id="36968-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="36968-227">`Timespan` , um zwischen versuchen zu warten, den Schlüssel Tresor auf Änderungen abzufragen.</span><span class="sxs-lookup"><span data-stu-id="36968-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="36968-228">Der Standardwert ist `null` (die Konfiguration wird nicht neu geladen).</span><span class="sxs-lookup"><span data-stu-id="36968-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="36968-229">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="36968-229">Use a key name prefix</span></span>

<span data-ttu-id="36968-230">Addazurekeyvault stellt eine Überladung bereit, die eine Implementierung von akzeptiert `Azure.Extensions.AspNetCore.Configuration.Secrets` , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="36968-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="36968-231">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="36968-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="36968-232">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="36968-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="36968-233">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="36968-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="36968-234">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="36968-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="36968-235">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="36968-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="36968-236">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="36968-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="36968-237">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="36968-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="36968-238">Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="36968-239">"Addazurekeyvault" wird mit einem benutzerdefinierten aufgerufen `Azure.Extensions.AspNetCore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="36968-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="36968-240">Die `Azure.Extensions.AspNetCore.Configuration.Secrets` Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="36968-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="36968-241">`Load` lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="36968-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="36968-242">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="36968-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="36968-243">`GetKey`:</span></span>
  * <span data-ttu-id="36968-244">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="36968-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="36968-245">Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="36968-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="36968-246">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="36968-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="36968-247">Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="36968-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="36968-248">Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="36968-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="36968-249">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="36968-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="36968-250">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="36968-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="36968-251">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="36968-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="36968-252">Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="36968-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="36968-253">Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="36968-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="36968-254">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="36968-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="36968-255">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="36968-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="36968-256">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="36968-257">Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="36968-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="36968-258">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="36968-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="36968-259">Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="36968-260">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .</span><span class="sxs-lookup"><span data-stu-id="36968-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="36968-261">Sie können auch eine eigene <xref:Azure.Security.KeyVault.Secrets.SecretClient> Implementierung für "addazurekeyvault" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="36968-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="36968-262">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="36968-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="36968-263">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="36968-263">Bind an array to a class</span></span>

<span data-ttu-id="36968-264">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="36968-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="36968-265">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="36968-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="36968-266">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="36968-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="36968-267">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="36968-268">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="36968-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="36968-269">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="36968-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="36968-270">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="36968-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="36968-271">Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken* reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="36968-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="36968-272">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="36968-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="36968-273">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="36968-273">Key</span></span> | <span data-ttu-id="36968-274">Wert</span><span class="sxs-lookup"><span data-stu-id="36968-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="36968-275">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="36968-275">Reload secrets</span></span>

<span data-ttu-id="36968-276">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="36968-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="36968-277">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="36968-278">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="36968-278">Disabled and expired secrets</span></span>

<span data-ttu-id="36968-279">Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="36968-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="36968-280">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="36968-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="36968-281">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="36968-281">Troubleshoot</span></span>

<span data-ttu-id="36968-282">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="36968-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="36968-283">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="36968-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="36968-284">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="36968-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="36968-285">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="36968-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="36968-286">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="36968-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="36968-287">Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="36968-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="36968-288">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="36968-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="36968-289">Die APP verfügt über den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ) oder Azure AD directoryid ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="36968-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="36968-290">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="36968-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="36968-291">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="36968-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36968-292">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36968-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="36968-293">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="36968-294">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="36968-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="36968-295">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="36968-296">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="36968-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="36968-297">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="36968-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="36968-298">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="36968-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36968-299">In diesem Dokument wird erläutert, wie Sie den [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Konfigurations Anbieter verwenden, um App-Konfigurationswerte aus Azure Key Vault geheimen Schlüsseln zu laden.</span><span class="sxs-lookup"><span data-stu-id="36968-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="36968-300">Azure Key Vault ist ein cloudbasierter Dienst, der Ihnen hilft, kryptografische Schlüssel und Geheimnisse zu schützen, die von apps und Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="36968-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="36968-301">Gängige Szenarien für die Verwendung von Azure Key Vault mit ASP.net Core-apps sind:</span><span class="sxs-lookup"><span data-stu-id="36968-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="36968-302">Steuern des Zugriffs auf vertrauliche Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="36968-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="36968-303">Erfüllen der Anforderung für die Überprüfung der Hardware Sicherheitsmodule (HSM) von PPS 140-2 Level 2 bei der Speicherung von Konfigurationsdaten.</span><span class="sxs-lookup"><span data-stu-id="36968-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="36968-304">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36968-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="36968-305">Pakete</span><span class="sxs-lookup"><span data-stu-id="36968-305">Packages</span></span>

<span data-ttu-id="36968-306">Fügen Sie einen Paket Verweis auf die [Microsoft.Extensions.Config-urung hinzu. Azurekeyvault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) -Paket.</span><span class="sxs-lookup"><span data-stu-id="36968-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="36968-307">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="36968-307">Sample app</span></span>

<span data-ttu-id="36968-308">Die Beispiel-APP wird in einem von zwei Modi ausgeführt, die durch die- `#define` Anweisung am Anfang der *Program.cs* -Datei bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="36968-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="36968-309">`Certificate`: Veranschaulicht die Verwendung einer Azure Key Vault Client-ID und eines X. 509-Zertifikats für den Zugriff auf in Azure Key Vault gespeicherte Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="36968-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="36968-310">Diese Version des Beispiels kann von einem beliebigen Speicherort aus ausgeführt werden, auf Azure App Service oder auf allen Hosts bereitgestellt werden, die eine ASP.net Core-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="36968-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="36968-311">`Managed`: Veranschaulicht, wie [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview) verwendet werden, um die APP für die Azure Key Vault mit Azure AD Authentifizierung ohne Anmelde Informationen zu authentifizieren, die im Code oder in der Konfiguration der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="36968-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="36968-312">Wenn Sie für die Authentifizierung verwaltete Identitäten verwenden, sind keine Azure AD Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="36968-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="36968-313">Die `Managed` Version des Beispiels muss in Azure bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="36968-314">Befolgen Sie die Anweisungen im Abschnitt [Verwenden der verwalteten Identitäten für Azure-Ressourcen](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="36968-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="36968-315">Weitere Informationen zum Konfigurieren einer Beispiel-App mithilfe von Präprozessordirektiven ( `#define` ) finden Sie unter <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="36968-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="36968-316">Speicherung von geheimen Schlüsseln in der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="36968-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="36968-317">Legen Sie Geheimnisse mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)lokal fest.</span><span class="sxs-lookup"><span data-stu-id="36968-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="36968-318">Wenn die Beispiel-App auf dem lokalen Computer in der Entwicklungsumgebung ausgeführt wird, werden die geheimen Schlüssel aus dem lokalen Benutzer Geheimnis Speicher geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="36968-319">Das Secret Manager-Tool erfordert eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="36968-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="36968-320">Legen Sie den-Eigenschafts Wert ( `{GUID}` ) auf eine eindeutige GUID fest:</span><span class="sxs-lookup"><span data-stu-id="36968-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="36968-321">Geheimnisse werden als Name-Wert-Paare erstellt.</span><span class="sxs-lookup"><span data-stu-id="36968-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="36968-322">Hierarchische Werte (Konfigurations Abschnitte) verwenden einen `:` (Doppelpunkt) als Trennzeichen in [ASP.net Core](xref:fundamentals/configuration/index) Namen von Konfigurations Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="36968-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="36968-323">Der geheime Manager wird von einer Befehlsshell verwendet, die für den [Inhalts](xref:fundamentals/index#content-root)Stamm des Projekts geöffnet ist, wobei `{SECRET NAME}` der Name und `{SECRET VALUE}` der Wert ist:</span><span class="sxs-lookup"><span data-stu-id="36968-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="36968-324">Führen Sie die folgenden Befehle in einer Befehlsshell aus dem [Inhalts](xref:fundamentals/index#content-root) Stamm des Projekts aus, um die geheimen Schlüssel für die Beispiel-App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="36968-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="36968-325">Wenn diese Geheimnisse in Azure Key Vault im [geheimen Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) gespeichert werden, wird das `_dev` Suffix in geändert `_prod` .</span><span class="sxs-lookup"><span data-stu-id="36968-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="36968-326">Das-Suffix bietet einen visuellen Hinweis in der APP-Ausgabe, die die Quelle der Konfigurationswerte anzeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="36968-327">Geheimer Speicher in der Produktionsumgebung mit Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="36968-328">Die Anweisungen im [Schnellstart: festlegen und Abrufen eines Geheimnisses aus Azure Key Vault mithilfe Azure CLI](/azure/key-vault/quick-create-cli) Themas werden hier zusammengefasst, um eine Azure Key Vault zu erstellen und Geheimnisse zu speichern, die von der Beispiel-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="36968-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="36968-329">Weitere Informationen finden Sie im Thema.</span><span class="sxs-lookup"><span data-stu-id="36968-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="36968-330">Öffnen Sie Azure Cloud Shell, indem Sie eine der folgenden Methoden in der [Azure-Portal](https://portal.azure.com/)verwenden:</span><span class="sxs-lookup"><span data-stu-id="36968-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="36968-331">Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.</span><span class="sxs-lookup"><span data-stu-id="36968-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="36968-332">Verwenden Sie die Such Zeichenfolge "Azure CLI" im Textfeld.</span><span class="sxs-lookup"><span data-stu-id="36968-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="36968-333">Öffnen Sie Cloud Shell in Ihrem Browser mit der Schaltfläche **Start Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="36968-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="36968-334">Wählen Sie im Menü in der oberen rechten Ecke des Azure-Portal die Schaltfläche **Cloud Shell** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="36968-335">Weitere Informationen finden Sie unter [Azure CLI](/cli/azure/) und [Übersicht über Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="36968-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="36968-336">Wenn Sie nicht bereits authentifiziert sind, melden Sie sich mit dem `az login` Befehl an.</span><span class="sxs-lookup"><span data-stu-id="36968-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="36968-337">Erstellen Sie eine Ressourcengruppe mit dem folgenden Befehl, wobei `{RESOURCE GROUP NAME}` der Name der Ressourcengruppe für die neue Ressourcengruppe und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="36968-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="36968-338">Erstellen Sie mit dem folgenden Befehl einen Schlüssel Tresor in der Ressourcengruppe, wobei `{KEY VAULT NAME}` der Name für den neuen Schlüssel Tresor und `{LOCATION}` die Azure-Region (Datacenter) ist:</span><span class="sxs-lookup"><span data-stu-id="36968-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="36968-339">Erstellen Sie Geheimnisse im Schlüssel Tresor als Name-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="36968-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="36968-340">Azure Key Vault geheimen Namen sind auf alphanumerische Zeichen und Bindestriche beschränkt.</span><span class="sxs-lookup"><span data-stu-id="36968-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="36968-341">Hierarchische Werte (Konfigurations Abschnitte) verwenden `--` (zwei Bindestriche) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="36968-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="36968-342">Doppelpunkte, die normalerweise verwendet werden, um einen Abschnitt von einem Unterschlüssel in [ASP.net Core Konfiguration](xref:fundamentals/configuration/index)zu begrenzen, sind in Key Vault-Geheimnis Namen nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="36968-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="36968-343">Aus diesem Grund werden zwei Bindestriche verwendet und für einen Doppelpunkt getauscht, wenn die geheimen Schlüssel in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="36968-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="36968-344">Die folgenden geheimen Schlüssel sind für die Verwendung mit der Beispiel-App vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="36968-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="36968-345">Die Werte enthalten ein `_prod` Suffix, um Sie von den suffixwerten zu unterscheiden, die `_dev` in der Entwicklungsumgebung von Benutzer Geheimnissen geladen werden.</span><span class="sxs-lookup"><span data-stu-id="36968-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="36968-346">Ersetzen `{KEY VAULT NAME}` Sie durch den Namen des Schlüssel Tresors, den Sie im vorherigen Schritt erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="36968-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="36968-347">Verwenden Sie die Anwendungs-ID und das X. 509-Zertifikat für nicht in Azure gehostete Apps.</span><span class="sxs-lookup"><span data-stu-id="36968-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="36968-348">Konfigurieren Sie Azure AD, Azure Key Vault und die APP für die Verwendung einer Azure Active Directory Anwendungs-ID und eines X. 509-Zertifikats, um sich bei einem Schlüssel Tresor zu authentifizieren, **Wenn die APP außerhalb von Azure gehostet wird**.</span><span class="sxs-lookup"><span data-stu-id="36968-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="36968-349">Weitere Informationen finden Sie im Artikel [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="36968-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="36968-350">Obwohl die Verwendung einer Anwendungs-ID und eines X. 509-Zertifikats für in Azure gehostete Apps unterstützt wird, empfiehlt es sich, beim Hosten einer APP in Azure [verwaltete Identitäten für Azure-Ressourcen zu](#use-managed-identities-for-azure-resources) verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="36968-351">Für verwaltete Identitäten ist das Speichern eines Zertifikats in der APP oder in der Entwicklungsumgebung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="36968-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="36968-352">Die Beispiel-App verwendet eine Anwendungs-ID und ein X. 509-Zertifikat, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="36968-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="36968-353">Erstellen Sie ein PKCS # 12-Archiv Zertifikat (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="36968-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="36968-354">Optionen zum Erstellen von Zertifikaten sind [Makecert unter Windows](/windows/desktop/seccrypto/makecert) und [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="36968-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="36968-355">Installieren Sie das Zertifikat im persönlichen Zertifikat Speicher des aktuellen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="36968-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="36968-356">Das Markieren des Schlüssels als exportierbar ist optional.</span><span class="sxs-lookup"><span data-stu-id="36968-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="36968-357">Notieren Sie den Fingerabdruck des Zertifikats, der später in diesem Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="36968-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="36968-358">Exportieren Sie das PKCS # 12-Archiv Zertifikat (*. pfx*) als ein-codiertes Zertifikat (*. CER*).</span><span class="sxs-lookup"><span data-stu-id="36968-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="36968-359">Registrieren Sie die APP bei Azure AD (**App-Registrierungen**).</span><span class="sxs-lookup"><span data-stu-id="36968-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="36968-360">Laden Sie das der-codierte Zertifikat (*. CER*) in Azure AD hoch:</span><span class="sxs-lookup"><span data-stu-id="36968-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="36968-361">Wählen Sie die app in Azure AD aus.</span><span class="sxs-lookup"><span data-stu-id="36968-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="36968-362">Navigieren Sie zu **Zertifikate & Geheimnissen**.</span><span class="sxs-lookup"><span data-stu-id="36968-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="36968-363">Wählen Sie **Zertifikat hochladen** aus, um das Zertifikat hochzuladen, das den öffentlichen Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="36968-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="36968-364">Ein *CER*-, *PEM*-oder *CRT* -Zertifikat ist akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="36968-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="36968-365">Speichern Sie den Key Vault-Namen, die Anwendungs-ID und den Zertifikat Fingerabdruck in der APP- *appsettings.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="36968-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="36968-366">Navigieren Sie in der Azure-Portal zu **Schlüssel Tresoren** .</span><span class="sxs-lookup"><span data-stu-id="36968-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="36968-367">Wählen Sie den Schlüssel Tresor aus, den Sie im Abschnitt " [Geheimnis Speicher in der Produktionsumgebung mit Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) " erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="36968-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="36968-368">Klicken Sie auf **Zugriffsrichtlinien**.</span><span class="sxs-lookup"><span data-stu-id="36968-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="36968-369">Wählen Sie **Zugriffsrichtlinie hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="36968-370">Öffnen Sie **geheime Berechtigungen** , und stellen Sie der APP die Berechtigungen **Get** und **List** bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="36968-371">Wählen Sie **Prinzipal auswählen** , und wählen Sie die registrierte App nach Name aus.</span><span class="sxs-lookup"><span data-stu-id="36968-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="36968-372">Wählen Sie die Schaltfläche **Auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="36968-373">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="36968-373">Select **OK**.</span></span>
1. <span data-ttu-id="36968-374">Wählen Sie **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="36968-374">Select **Save**.</span></span>
1. <span data-ttu-id="36968-375">Stellen Sie die App bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-375">Deploy the app.</span></span>

<span data-ttu-id="36968-376">Die `Certificate` Beispiel-APP erhält Ihre Konfigurationswerte aus `IConfigurationRoot` dem gleichen Namen wie der geheime Name:</span><span class="sxs-lookup"><span data-stu-id="36968-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="36968-377">Nicht hierarchische Werte: der Wert für `SecretName` wird mit abgerufen `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="36968-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="36968-378">Hierarchische Werte (Abschnitte): Verwenden Sie `:` die Notation (Doppelpunkt) oder die `GetSection` Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="36968-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="36968-379">Verwenden Sie einen dieser Ansätze zum Abrufen des Konfigurations Werts:</span><span class="sxs-lookup"><span data-stu-id="36968-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="36968-380">Das X. 509-Zertifikat wird vom Betriebssystem verwaltet.</span><span class="sxs-lookup"><span data-stu-id="36968-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="36968-381">Die App Ruft <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> mit Werten auf, die von der Datei bereitgestellt werden *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="36968-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="36968-382">Beispielwerte:</span><span class="sxs-lookup"><span data-stu-id="36968-382">Example values:</span></span>

* <span data-ttu-id="36968-383">Key Vault-Name: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="36968-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="36968-384">Anwendungs-ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="36968-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="36968-385">Zertifikat Fingerabdruck: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="36968-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="36968-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36968-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="36968-387">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="36968-388">In der Entwicklungsumgebung werden geheime Werte mit dem- `_dev` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="36968-389">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="36968-390">Verwenden von verwalteten Identitäten für Azure-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36968-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="36968-391">**Eine in Azure** bereitgestellte App kann [verwaltete Identitäten für Azure-Ressourcen](/azure/active-directory/managed-identities-azure-resources/overview)nutzen, die es der APP ermöglichen, sich mit Azure Key Vault zu authentifizieren, indem Sie Azure AD Authentifizierung ohne Anmelde Informationen (Anwendungs-ID und Kennwort/geheimer Client Schlüssel) verwenden, die in der APP gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="36968-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="36968-392">In der Beispiel-App werden verwaltete Identitäten für Azure-Ressourcen verwendet, wenn die- `#define` Anweisung am Anfang der *Program.cs* -Datei auf festgelegt ist `Managed` .</span><span class="sxs-lookup"><span data-stu-id="36968-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="36968-393">Geben Sie den Tresor Namen in die Datei der APP ein *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36968-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="36968-394">Die Beispiel-App erfordert keine Anwendungs-ID und kein Kennwort (geheimer Client Schlüssel), wenn Sie auf die-Version festgelegt `Managed` ist, sodass Sie diese Konfigurationseinträge ignorieren können.</span><span class="sxs-lookup"><span data-stu-id="36968-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="36968-395">Die APP wird in Azure bereitgestellt, und Azure authentifiziert die APP für den Zugriff auf Azure Key Vault nur mithilfe des Tresor namens, der in der Datei gespeichert ist *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="36968-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="36968-396">Stellen Sie die Beispiel-App für Azure App Service bereit.</span><span class="sxs-lookup"><span data-stu-id="36968-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="36968-397">Eine APP, die für Azure App Service bereitgestellt wird, wird automatisch bei Azure AD registriert, wenn der Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="36968-398">Rufen Sie die Objekt-ID aus der Bereitstellung ab, um Sie im folgenden Befehl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="36968-399">Die Objekt-ID wird in der Azure-Portal im Bereich der **Identity** App Service angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="36968-400">Wenn Sie Azure CLI und die Objekt-ID der App verwenden, stellen Sie der APP die `list` `get` Berechtigungen und für den Zugriff auf den Schlüssel Tresor bereit:</span><span class="sxs-lookup"><span data-stu-id="36968-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="36968-401">**Starten Sie die APP** mit Azure CLI, PowerShell oder der Azure-Portal neu.</span><span class="sxs-lookup"><span data-stu-id="36968-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="36968-402">Die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="36968-402">The sample app:</span></span>

* <span data-ttu-id="36968-403">Erstellt eine Instanz der- `AzureServiceTokenProvider` Klasse ohne eine Verbindungs Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="36968-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="36968-404">Wenn keine Verbindungs Zeichenfolge bereitgestellt wird, versucht der Anbieter, ein Zugriffs Token von verwalteten Identitäten für Azure-Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="36968-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="36968-405">Ein neues <xref:Microsoft.Azure.KeyVault.KeyVaultClient> wird mit dem `AzureServiceTokenProvider` instanztokenrückruf erstellt.</span><span class="sxs-lookup"><span data-stu-id="36968-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="36968-406">Die- <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Instanz wird mit einer Standard Implementierung von verwendet <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , die alle geheimen Werte lädt und doppelte Bindestriche ( `--` ) durch Doppelpunkte ( `:` ) in Schlüsselnamen ersetzt.</span><span class="sxs-lookup"><span data-stu-id="36968-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="36968-407">Beispiel Wert für Key Vault-Name: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="36968-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="36968-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="36968-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="36968-409">Wenn Sie die app ausführen, werden auf einer Webseite die geladenen geheimen Werte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="36968-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="36968-410">In der Entwicklungsumgebung verfügen geheime Werte über das- `_dev` Suffix, da Sie von Benutzer Geheimnissen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="36968-411">In der Produktionsumgebung werden die Werte mit dem- `_prod` Suffix geladen, da Sie von Azure Key Vault bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="36968-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="36968-412">Wenn Sie einen `Access denied` Fehler erhalten, vergewissern Sie sich, dass die APP mit Azure AD registriert wurde und Zugriff auf den Schlüssel Tresor hat.</span><span class="sxs-lookup"><span data-stu-id="36968-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="36968-413">Vergewissern Sie sich, dass Sie den Dienst in Azure neu gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="36968-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="36968-414">Informationen zur Verwendung des Anbieters mit einer verwalteten Identität und einer Azure devops-Pipeline finden Sie unter [Erstellen einer Azure Resource Manager Dienst Verbindung mit einem virtuellen Computer mit einer verwalteten Dienst Identität](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="36968-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="36968-415">Schlüsselnamen Präfix verwenden</span><span class="sxs-lookup"><span data-stu-id="36968-415">Use a key name prefix</span></span>

<span data-ttu-id="36968-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> stellt eine Überladung bereit, die eine Implementierung von akzeptiert <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , mit der Sie steuern können, wie Key Vault-Geheimnisse in Konfigurationsschlüssel konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="36968-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="36968-417">Beispielsweise können Sie die-Schnittstelle implementieren, um geheime Werte basierend auf einem Präfix Wert zu laden, den Sie beim Starten der APP bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="36968-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="36968-418">So können Sie z. b. geheime Schlüssel basierend auf der Version der App laden.</span><span class="sxs-lookup"><span data-stu-id="36968-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="36968-419">Verwenden Sie keine Präfixe für Key Vault-Geheimnisse, um geheime Schlüssel für mehrere apps in demselben Schlüssel Tresor zu platzieren oder um Umwelt Geheimnisse (z. b. *Entwicklungs* -und *Produktions* Geheimnisse) in demselben Tresor zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="36968-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="36968-420">Es wird empfohlen, dass unterschiedliche apps und Entwicklungs-/Produktionsumgebungen separate Schlüssel Tresore verwenden, um App-Umgebungen für die höchste Sicherheitsstufe zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="36968-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="36968-421">Im folgenden Beispiel wird ein geheimer Schlüssel im Schlüssel Tresor erstellt (und mit dem Geheimnis-Manager-Tool für die Entwicklungsumgebung) für `5000-AppSecret` (Zeiträume sind in Key Vault-Geheimnis Namen nicht zulässig).</span><span class="sxs-lookup"><span data-stu-id="36968-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="36968-422">Dieser geheime Schlüssel stellt einen geheimen App-Schlüssel für die Version 5.0.0.0 der APP dar.</span><span class="sxs-lookup"><span data-stu-id="36968-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="36968-423">Bei einer anderen Version der APP, 5.1.0.0, wird dem Schlüssel Tresor ein geheimer Schlüssel (und mit dem Geheimnis-Manager-Tool) für hinzugefügt `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="36968-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="36968-424">Jede APP-Version lädt den Wert für die Versionierung mit Versions Angabe in die Konfiguration `AppSecret` , wobei die Version beim Laden des geheimen Schlüssels entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="36968-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> wird mit einem benutzerdefinierten aufgerufen <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="36968-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="36968-426">Die <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> Implementierung reagiert auf die Versions Präfixe von Geheimnissen, um den richtigen geheimen Schlüssel in die Konfiguration zu laden:</span><span class="sxs-lookup"><span data-stu-id="36968-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="36968-427">`Load` lädt ein Geheimnis, wenn sein Name mit dem Präfix beginnt.</span><span class="sxs-lookup"><span data-stu-id="36968-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="36968-428">Andere Geheimnisse werden nicht geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="36968-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="36968-429">`GetKey`:</span></span>
  * <span data-ttu-id="36968-430">Entfernt das Präfix aus dem Namen des geheimen Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="36968-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="36968-431">Ersetzt zwei Bindestriche in einem beliebigen Namen durch das-Zeichen `KeyDelimiter` , das das in der Konfiguration verwendete Trennzeichen (normalerweise ein Doppelpunkt) ist.</span><span class="sxs-lookup"><span data-stu-id="36968-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="36968-432">Azure Key Vault lässt keinen Doppelpunkt in geheimen Namen zu.</span><span class="sxs-lookup"><span data-stu-id="36968-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="36968-433">Die- `Load` Methode wird von einem Anbieter Algorithmus aufgerufen, der die geheimen Schlüssel des Tresors durchläuft, um diejenigen zu finden, die über das Versions Präfix verfügen.</span><span class="sxs-lookup"><span data-stu-id="36968-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="36968-434">Wenn ein Versions Präfix mit gefunden wird `Load` , verwendet der Algorithmus die- `GetKey` Methode, um den Konfigurations Namen des geheimen namens zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="36968-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="36968-435">Es entfernt das Versions Präfix aus dem Namen des Geheimnisses und gibt den restlichen geheimen Namen für das Laden in die Konfigurations Name-Wert-Paare der APP zurück.</span><span class="sxs-lookup"><span data-stu-id="36968-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="36968-436">Wenn dieser Ansatz implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="36968-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="36968-437">Die in der Projektdatei der APP angegebene Version der app.</span><span class="sxs-lookup"><span data-stu-id="36968-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="36968-438">Im folgenden Beispiel wird die App-Version auf festgelegt `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="36968-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="36968-439">Vergewissern Sie sich, dass eine `<UserSecretsId>` Eigenschaft in der Projektdatei der app vorhanden ist, wobei `{GUID}` eine vom Benutzer bereitgestellte GUID ist:</span><span class="sxs-lookup"><span data-stu-id="36968-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="36968-440">Speichern Sie die folgenden geheimen Schlüssel lokal mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="36968-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="36968-441">Geheime Schlüssel werden in Azure Key Vault mithilfe der folgenden Azure CLI Befehle gespeichert:</span><span class="sxs-lookup"><span data-stu-id="36968-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="36968-442">Wenn die app ausgeführt wird, werden die geheimen Schlüssel Tresor-Schlüssel geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="36968-443">Der Zeichen folgen Schlüssel für `5000-AppSecret` wird mit der in der Projektdatei der APP () angegebenen Version der APP abgeglichen `5.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="36968-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="36968-444">Die Version `5000` (mit dem Bindestrich) wird aus dem Schlüsselnamen entfernt.</span><span class="sxs-lookup"><span data-stu-id="36968-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="36968-445">Während der gesamten APP wird beim Lesen der Konfiguration mit dem Schlüssel `AppSecret` der geheime Wert geladen.</span><span class="sxs-lookup"><span data-stu-id="36968-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="36968-446">Wenn die App-Version in der Projektdatei in geändert wird `5.1.0.0` und die APP erneut ausgeführt wird, wird der geheime Wert `5.1.0.0_secret_value_dev` in der Entwicklungsumgebung und in der Produktionsumgebung zurückgegeben `5.1.0.0_secret_value_prod` .</span><span class="sxs-lookup"><span data-stu-id="36968-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="36968-447">Sie können auch eine eigene <xref:Microsoft.Azure.KeyVault.KeyVaultClient> Implementierung für bereitstellen <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="36968-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="36968-448">Ein benutzerdefinierter Client ermöglicht die gemeinsame Nutzung einer einzelnen Instanz des Clients in der gesamten app.</span><span class="sxs-lookup"><span data-stu-id="36968-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="36968-449">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="36968-449">Bind an array to a class</span></span>

<span data-ttu-id="36968-450">Der Anbieter ist in der Lage, Konfigurationswerte in ein Array für die Bindung an ein poco-Array zu lesen.</span><span class="sxs-lookup"><span data-stu-id="36968-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="36968-451">Beim Lesen aus einer Konfigurations Quelle, bei der Schlüssel Trennzeichen ( `:` Trennzeichen) enthalten können, wird ein numerisches Schlüssel Segment verwendet, um die Schlüssel zu unterscheiden, die ein Array bilden ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="36968-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="36968-452">Weitere Informationen finden Sie unter [Konfiguration: Binden eines Arrays an eine Klasse](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="36968-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="36968-453">Azure Key Vault Schlüssel können keinen Doppelpunkt als Trennzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="36968-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="36968-454">Der in diesem Thema beschriebene Ansatz verwendet doppelte Bindestriche ( `--` ) als Trennzeichen für hierarchische Werte (Abschnitte).</span><span class="sxs-lookup"><span data-stu-id="36968-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="36968-455">Array Schlüssel werden in Azure Key Vault mit doppelten Bindestrichen und numerischen Schlüsselsegmenten ( `--0--` , `--1--` ,) gespeichert &hellip; `--{n}--` .</span><span class="sxs-lookup"><span data-stu-id="36968-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="36968-456">Überprüfen Sie die folgende Konfiguration des [seriprotokollierungs](https://serilog.net/) Anbieters, die von einer JSON-Datei bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="36968-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="36968-457">Im Array sind zwei Objektliterale definiert `WriteTo` , die zwei serilog- *senken* reflektieren, die Ziele für die Protokollierungs Ausgabe beschreiben:</span><span class="sxs-lookup"><span data-stu-id="36968-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="36968-458">Die in der vorangehenden JSON-Datei angezeigte Konfiguration wird in Azure Key Vault mithilfe von Double Dash ( `--` )-Notation und numerischen Segmenten gespeichert:</span><span class="sxs-lookup"><span data-stu-id="36968-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="36968-459">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="36968-459">Key</span></span> | <span data-ttu-id="36968-460">Wert</span><span class="sxs-lookup"><span data-stu-id="36968-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="36968-461">Geheimnisse erneut laden</span><span class="sxs-lookup"><span data-stu-id="36968-461">Reload secrets</span></span>

<span data-ttu-id="36968-462">Geheimnisse werden zwischengespeichert, bis `IConfigurationRoot.Reload()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="36968-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="36968-463">Abgelaufene, deaktivierte und aktualisierte geheime Schlüssel im Schlüssel Tresor werden von der APP erst berücksichtigt, wenn der `Reload` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="36968-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="36968-464">Deaktivierte und abgelaufene Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="36968-464">Disabled and expired secrets</span></span>

<span data-ttu-id="36968-465">Deaktivierte und abgelaufene Geheimnisse lösen einen aus <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="36968-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="36968-466">Um zu verhindern, dass die APP ausgelöst wird, stellen Sie die Konfiguration mithilfe eines anderen Konfigurations Anbieters bereit, oder aktualisieren Sie das deaktivierte oder abgelaufene Geheimnis.</span><span class="sxs-lookup"><span data-stu-id="36968-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="36968-467">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="36968-467">Troubleshoot</span></span>

<span data-ttu-id="36968-468">Wenn die APP die Konfiguration mit dem Anbieter nicht laden kann, wird eine Fehlermeldung in die [ASP.net Core Protokollierungs Infrastruktur](xref:fundamentals/logging/index)geschrieben.</span><span class="sxs-lookup"><span data-stu-id="36968-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="36968-469">Die folgenden Bedingungen verhindern, dass die Konfiguration geladen wird:</span><span class="sxs-lookup"><span data-stu-id="36968-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="36968-470">Die APP oder das Zertifikat ist in Azure Active Directory nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="36968-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="36968-471">Der Schlüssel Tresor ist nicht in Azure Key Vault vorhanden.</span><span class="sxs-lookup"><span data-stu-id="36968-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="36968-472">Die APP ist nicht autorisiert, auf den Schlüssel Tresor zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="36968-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="36968-473">Die Zugriffs Richtlinie enthält nicht `Get` die `List` Berechtigungen und.</span><span class="sxs-lookup"><span data-stu-id="36968-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="36968-474">Im Schlüssel Tresor werden die Konfigurationsdaten (Name/Wert-Paar) fälschlicherweise benannt, fehlen, sind deaktiviert oder abgelaufen.</span><span class="sxs-lookup"><span data-stu-id="36968-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="36968-475">Die APP hat den falschen Schlüssel Tresor Namen ( `KeyVaultName` ), Azure AD Anwendungs-ID ( `AzureADApplicationId` ) oder Azure AD Zertifikat Fingerabdruck ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="36968-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="36968-476">Der Konfigurationsschlüssel (Name) ist in der APP falsch für den Wert, den Sie laden möchten.</span><span class="sxs-lookup"><span data-stu-id="36968-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="36968-477">Beim Hinzufügen der Zugriffs Richtlinie für die APP zum Schlüssel Tresor wurde die Richtlinie erstellt, aber die Schaltfläche **Speichern** wurde nicht in der Benutzeroberfläche für **Zugriffsrichtlinien** ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="36968-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36968-478">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36968-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="36968-479">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="36968-480">Microsoft Azure: Key Vault-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="36968-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="36968-481">Generieren und übertragen von HSM-geschützten Schlüsseln für Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="36968-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="36968-482">Keyvaultclient-Klasse</span><span class="sxs-lookup"><span data-stu-id="36968-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="36968-483">Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App</span><span class="sxs-lookup"><span data-stu-id="36968-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="36968-484">Tutorial: Verwenden von Azure Key Vault mit einem virtuellen Azure-Windows-Computer in .NET</span><span class="sxs-lookup"><span data-stu-id="36968-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
