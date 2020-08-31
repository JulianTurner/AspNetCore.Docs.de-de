---
title: Erweiterbarkeit der Schlüsselverwaltung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Erweiterbarkeit von ASP.net Core Datenschutz Schlüsselverwaltung.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: db718b8d4c305b75ad52054efde6b2d03f6825ed
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153531"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Erweiterbarkeit der Schlüsselverwaltung in ASP.net Core

Lesen Sie den Abschnitt [Key Management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) vor dem Lesen dieses Abschnitts, da hier einige der grundlegenden Konzepte hinter diesen APIs erläutert werden.

**Warnung**: Typen, die eine der folgenden Schnittstellen implementieren, sollten für mehrere Aufrufer Thread sicher sein.

## <a name="key"></a>Key

Die- `IKey` Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Cryptosystem. Der Begriff Key wird hier im abstrakten Sinn verwendet, nicht im literalsinn von "kryptografieschlüsselmaterial". Ein Schlüssel weist die folgenden Eigenschaften auf:

* Aktivierungs-, Erstellungs-und Ablaufdatum

* Sperrstatus

* Schlüssel Bezeichner (GUID)

::: moniker range=">= aspnetcore-2.0"

Darüber hinaus macht eine-Methode verfügbar, `IKey` `CreateEncryptor` die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Darüber hinaus macht eine-Methode verfügbar, `IKey` `CreateEncryptorInstance` die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.

::: moniker-end

> [!NOTE]
> Es gibt keine API, um das unformatierte kryptografiematerial aus einer- `IKey` Instanz abzurufen.

## <a name="ikeymanager"></a>Ikeymanager

Die- `IKeyManager` Schnittstelle stellt ein Objekt dar, das für die Speicherung, den Abruf und die Bearbeitung allgemeiner Schlüssel zuständig ist Es macht drei Vorgänge auf hoher Ebene verfügbar:

* Erstellen Sie einen neuen Schlüssel, und speichern Sie ihn im Speicher.

* Alle Schlüssel aus dem Speicher erhalten.

* Widerrufen Sie mindestens einen Schlüssel, und speichern Sie die Sperrinformationen im Speicher.

>[!WARNING]
> Das Schreiben eines `IKeyManager` ist eine sehr fortschrittliche Aufgabe, und die Mehrzahl der Entwickler sollten es nicht versuchen. Stattdessen sollten die meisten Entwickler die Funktionen nutzen, die von der [xmlkeymanager](#xmlkeymanager) -Klasse angeboten werden.

## <a name="xmlkeymanager"></a>Xmlkeymanager

Der `XmlKeyManager` -Typ ist die in-Box konkrete Implementierung von `IKeyManager` . Es bietet mehrere nützliche Funktionen, einschließlich Schlüssel Hinterlegung und Verschlüsselung von Schlüsseln im Ruhezustand. Schlüssel in diesem System werden als XML-Elemente (insbesondere [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) dargestellt.

`XmlKeyManager` hängt von verschiedenen anderen Komponenten im Rahmen der Erfüllung der Aufgaben ab:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, mit dem die von neuen Schlüsseln verwendeten Algorithmen bestimmt werden.

* `IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor` [optional], mit dem Schlüssel im Ruhezustand verschlüsselt werden können.

* `IKeyEscrowSink` [optional], die Schlüssel Hinterlegungs Dienste bereitstellt.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor` [optional], mit dem Schlüssel im Ruhezustand verschlüsselt werden können.

* `IKeyEscrowSink` [optional], die Schlüssel Hinterlegungs Dienste bereitstellt.

::: moniker-end

Im folgenden finden Sie Diagramme auf hoher Ebene, die angeben, wie diese Komponenten in miteinander verbunden sind `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Schlüssel Erstellung](key-management/_static/keycreation2.png)

*Key Creation/kreatenewkey*

In der Implementierung von `CreateNewKey` wird die- `AlgorithmConfiguration` Komponente verwendet, um einen eindeutigen zu erstellen `IAuthenticatedEncryptorDescriptor` , der dann als XML serialisiert wird. Wenn eine Schlüssel Hinterlegungs Senke vorhanden ist, wird die unverschlüsselte (unverschlüsselte) XML-Daten für die langfristige Speicherung an die Senke bereitgestellt. Der unverschlüsselte XML-Code wird dann `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über den persistent gespeichert `IXmlRepository` . (Wenn kein `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument in gespeichert `IXmlRepository` .)

![Schlüssel Abruf](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel Erstellung](key-management/_static/keycreation1.png)

*Key Creation/kreatenewkey*

In der Implementierung von `CreateNewKey` wird die- `IAuthenticatedEncryptorConfiguration` Komponente verwendet, um einen eindeutigen zu erstellen `IAuthenticatedEncryptorDescriptor` , der dann als XML serialisiert wird. Wenn eine Schlüssel Hinterlegungs Senke vorhanden ist, wird die unverschlüsselte (unverschlüsselte) XML-Daten für die langfristige Speicherung an die Senke bereitgestellt. Der unverschlüsselte XML-Code wird dann `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über den persistent gespeichert `IXmlRepository` . (Wenn kein `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument in gespeichert `IXmlRepository` .)

![Schlüssel Abruf](key-management/_static/keyretrieval1.png)

::: moniker-end

*Schlüssel Abruf/getallkeys*

In der Implementierung von `GetAllKeys` werden die XML-Dokumente, die Schlüssel und widerrufen darstellen, aus der zugrunde liegenden gelesen `IXmlRepository` . Wenn diese Dokumente verschlüsselt sind, werden Sie vom System automatisch entschlüsselt. `XmlKeyManager` erstellt die entsprechenden- `IAuthenticatedEncryptorDescriptorDeserializer` Instanzen, um die Dokumente wieder in Instanzen zu deserialisieren `IAuthenticatedEncryptorDescriptor` , die anschließend in einzelne Instanzen umschließt werden `IKey` . Diese Auflistung von- `IKey` Instanzen wird an den Aufrufer zurückgegeben.

Weitere Informationen zu den einzelnen XML-Elementen finden Sie im [Dokument Schlüsselspeicher Format](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>Ixmlrepository

Die- `IXmlRepository` Schnittstelle stellt einen Typ dar, der XML in einem Sicherungs Speicher beibehalten und XML abrufen kann. Es macht zwei APIs verfügbar:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementierungen von `IXmlRepository` müssen den XML-Code nicht analysieren. Sie sollten die XML-Dokumente als nicht transparent behandeln und größere Ebenen beim Erstellen und Verarbeiten der Dokumente berücksichtigen.

Es gibt vier integrierte, konkrete Typen, die implementieren `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [Filesystemxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [Registryxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [Azurestorage. azureblobxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [Redisxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [Filesystemxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [Registryxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [Azurestorage. azureblobxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [Redisxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Weitere Informationen finden Sie im [Dokument Schlüsselspeicher Anbieter](xref:security/data-protection/implementation/key-storage-providers) .

Die Registrierung eines benutzerdefinierten `IXmlRepository` ist sinnvoll, wenn ein anderer Sicherungs Speicher verwendet wird (z. b. Azure Table Storage).

Registrieren Sie eine benutzerdefinierte-Instanz, um das standardrepository Anwendungs weit zu ändern `IXmlRepository` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a>Ixmlencryptor

Die- `IXmlEncryptor` Schnittstelle stellt einen Typ dar, der ein Klartext-XML-Element verschlüsseln kann. Es macht eine einzelne API verfügbar:

* Verschlüsseln (XElement Klartext): verschlüsseltedxmlinfo

Wenn eine serialisierte `IAuthenticatedEncryptorDescriptor` Elemente enthält, die als "erfordert Verschlüsselung" gekennzeichnet sind, `XmlKeyManager` werden diese Elemente von durch die `IXmlEncryptor` -Methode der konfigurierten-Methode ausgeführt `Encrypt` , und es wird das enchiffrierte-Element und nicht das Klartext-Element in `IXmlRepository` persistent gespeichert. Bei der Ausgabe der- `Encrypt` Methode handelt es sich um ein- `EncryptedXmlInfo` Objekt. Bei diesem Objekt handelt es sich um einen Wrapper, der sowohl das resultierende chiffrierte `XElement` Element als auch den Typ enthält, der einen darstellt `IXmlDecryptor` , mit dem das entsprechende Element entschlüsselt werden kann.

Es gibt vier integrierte, konkrete Typen, die implementieren `IXmlEncryptor` :

* [Certifi-exmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [Dpapingxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [Dpapixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [Nullxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Weitere Informationen finden Sie im [Dokument Key Encryption at Rest](xref:security/data-protection/implementation/key-encryption-at-rest) .

Registrieren Sie eine benutzerdefinierte-Instanz, um den standardmäßigen Rest-Mechanismus für die Schlüssel Verschlüsselung zu ändern `IXmlEncryptor` :

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a>Ixmldecryptor

Die- `IXmlDecryptor` Schnittstelle stellt einen Typ dar, der weiß, wie ein entschlüsselt wird `XElement` , das über einen verschlüsselt wurde `IXmlEncryptor` . Es macht eine einzelne API verfügbar:

* Entschlüsseln (XElement-verschlüsseltedelement): XElement

Die- `Decrypt` Methode macht die von ausgeführte Verschlüsselung unerledigt `IXmlEncryptor.Encrypt` . Im allgemeinen verfügt jede konkrete `IXmlEncryptor` Implementierung über eine entsprechende konkrete `IXmlDecryptor` Implementierung.

Typen, die implementieren, `IXmlDecryptor` sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> Der `IServiceProvider` an den Konstruktor übergeben kann NULL sein.

## <a name="ikeyescrowsink"></a>Ikeyescrowsink

Die- `IKeyEscrowSink` Schnittstelle stellt einen Typ dar, der die Hinterlegung vertraulicher Informationen durchführen kann. Beachten Sie, dass serialisierte Deskriptoren vertrauliche Informationen (z. b. kryptografische Materialien) enthalten können. Dies hat zur Einführung des Typs [ixmlencryptor](#ixmlencryptor) geführt. Es treten jedoch Fehler auf, und Schlüssel Ringe können gelöscht oder beschädigt werden.

Die Hinterlegungs Schnittstelle stellt eine Notfall-escapeschraffur dar und ermöglicht den Zugriff auf die unformatierte serialisierte XML, bevor Sie von einem konfigurierten [ixmlencryptor](#ixmlencryptor)transformiert wird. Die-Schnittstelle macht eine einzelne API verfügbar:

* Store (GUID-Schlüssel-ID, XElement-Element)

Es liegt an der `IKeyEscrowSink` Implementierung, das bereitgestellte Element auf sichere Weise in Übereinstimmung mit der Geschäfts Richtlinie zu verarbeiten. Eine mögliche Implementierung könnte sein, dass die hinterlegte Senke das XML-Element mit einem bekannten Unternehmens-X. 509-Zertifikat verschlüsselt, bei dem der private Schlüssel des Zertifikats verwendet wurde. der `CertificateXmlEncryptor` Typ kann dies unterstützen. Die- `IKeyEscrowSink` Implementierung ist auch dafür verantwortlich, das bereitgestellte-Element entsprechend beizubehalten.

Standardmäßig ist kein Hinterlegungs Mechanismus aktiviert, aber Server Administratoren können [Dies Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy). Sie kann auch Programm gesteuert über die-Methode konfiguriert werden, `IDataProtectionBuilder.AddKeyEscrowSink` wie im folgenden Beispiel gezeigt. Die `AddKeyEscrowSink` -Methoden Überladungen spiegeln die `IServiceCollection.AddSingleton` -und- `IServiceCollection.AddInstance` über Ladungen wider, da-Instanzen als Single `IKeyEscrowSink` Tons gedacht sind. Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, werden diese während der Schlüsselgenerierung aufgerufen, sodass Schlüssel gleichzeitig an mehrere Mechanismen zusammengestellt werden können.

Es gibt keine API zum Lesen von Material aus einer `IKeyEscrowSink` Instanz. Dies entspricht der Entwurfs Theorie des Hinterlegungs Mechanismus: Sie soll das Schlüsselmaterial für eine vertrauenswürdige Zertifizierungsstelle zugänglich machen, und da die Anwendung selbst keine vertrauenswürdige Autorität ist, sollte Sie keinen Zugriff auf Ihr eigenes Material haben.

Der folgende Beispielcode veranschaulicht das Erstellen und Registrieren eines-Schlüssels `IKeyEscrowSink` , bei dem Schlüssel gespeichert werden, sodass nur Mitglieder von "condesodomain Admins" diese wiederherstellen können.

> [!NOTE]
> Zum Ausführen dieses Beispiels müssen Sie sich auf einem in die Domäne eingebundenen Windows 8/Windows Server 2012-Computer befinden, und der Domänen Controller muss Windows Server 2012 oder höher sein.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
