---
title: Benutzerdefinierte Autorisierungs Richtlinien Anbieter in ASP.net Core
author: mjrousos
description: Erfahren Sie, wie Sie einen benutzerdefinierten iauthorizationpolicyprovider in einer ASP.net Core-App verwenden, um Autorisierungs Richtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053136"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Benutzerdefinierte Autorisierungs Richtlinien Anbieter, die iauthorizationpolicyprovider in ASP.net Core verwenden 

Von [Mike Rousos](https://github.com/mjrousos)

Wenn Sie die [Richtlinien basierte Autorisierung](xref:security/authorization/policies)verwenden, werden die Richtlinien in der Regel durch Aufrufen von `AuthorizationOptions.AddPolicy` als Teil der Autorisierungs Dienst Konfiguration registriert. In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), Alle Autorisierungs Richtlinien auf diese Weise zu registrieren. In diesen Fällen können Sie [eine benutzerdefinierte `IAuthorizationPolicyProvider` verwenden](#ci) , um zu steuern, wie Autorisierungs Richtlinien bereitgestellt werden.

Beispiele für Szenarien, in denen ein benutzerdefinierter [iauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann, sind:

* Verwenden eines externen Dienstanbieter zur Bereitstellung der Richtlinien Auswertung.
* Die Verwendung einer großen Bandbreite an Richtlinien (z. b. für unterschiedliche Raum Nummern oder Alters Zahlen), daher ist es nicht sinnvoll, jede einzelne Autorisierungs Richtlinie mit einem-Befehl hinzuzufügen `AuthorizationOptions.AddPolicy` .
* Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. b. einer Datenbank) oder durch das dynamische ermitteln der Autorisierungs Anforderungen über einen anderen Mechanismus

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) aus dem [aspnetcore-GitHub-Repository](https://github.com/dotnet/AspNetCore). Laden Sie die dotnet/aspnetcore-Repository-ZIP-Datei herunter. Entzippen Sie die Datei. Navigieren Sie zum Projektordner *src/Security/Samples/custompolicyprovider* .

## <a name="customize-policy-retrieval"></a>Anpassen des Richtlinien Abrufs

ASP.net Core-Apps verwenden eine Implementierung der- `IAuthorizationPolicyProvider` Schnittstelle zum Abrufen von Autorisierungs Richtlinien. Standardmäßig ist [defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und wird verwendet. `DefaultAuthorizationPolicyProvider` gibt Richtlinien aus der in einem-Befehl zurück `AuthorizationOptions` `IServiceCollection.AddAuthorization` .

Passen Sie dieses Verhalten an, indem Sie eine andere `IAuthorizationPolicyProvider` Implementierung im Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der APP registrieren. 

Die `IAuthorizationPolicyProvider` Schnittstelle enthält drei APIs:

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungs Richtlinie für einen angegebenen Namen zurück.
* [Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standard Autorisierungs Richtlinie zurück (die Richtlinie, die für `[Authorize]` Attribute ohne angegebene Richtlinie verwendet wird). 
* [Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) gibt die Fall Back Autorisierungs Richtlinie zurück (die Richtlinie, die von der Autorisierungs Middleware verwendet wird, wenn keine Richtlinie angegeben ist). 

Durch Implementieren dieser APIs können Sie die Art der Bereitstellung von Autorisierungs Richtlinien anpassen.

## <a name="parameterized-authorize-attribute-example"></a>Beispiel für ein parametrisiertes Autorisierungs Attribut

Ein Szenario, in dem nützlich ist, `IAuthorizationPolicyProvider` ist das Aktivieren benutzerdefinierter `[Authorize]` Attribute, deren Anforderungen von einem Parameter abhängen. Beispielsweise wurde in der Dokumentation zur [Richtlinien basierten Autorisierung](xref:security/authorization/policies) eine Alters basierte Richtlinie ("AtLeast21") als Beispiel verwendet. Wenn verschiedene Controller Aktionen in einer APP für Benutzer unter *schiedlicher* Zeiten verfügbar gemacht werden sollen, kann es hilfreich sein, viele verschiedene Alters basierte Richtlinien zu haben. Anstatt alle unterschiedlichen Alters basierten Richtlinien zu registrieren, die von der Anwendung benötigt werden `AuthorizationOptions` , können Sie die Richtlinien dynamisch mit einem benutzerdefinierten generieren `IAuthorizationPolicyProvider` . Um die Verwendung der Richtlinien zu vereinfachen, können Sie Aktionen mit benutzerdefiniertem Autorisierungs Attribut wie mit Anmerkungen versehen `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>Benutzerdefinierte Autorisierungs Attribute

Autorisierungs Richtlinien werden anhand ihrer Namen identifiziert. Der `MinimumAgeAuthorizeAttribute` zuvor beschriebene benutzerdefinierte muss Argumente einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungs Richtlinie verwendet werden kann. Dies können Sie erreichen, indem Sie von ableiten `AuthorizeAttribute` und die- `Age` Eigenschaft in die-Eigenschaft einbinden `AuthorizeAttribute.Policy` .

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

Dieser Attributtyp hat eine `Policy` Zeichenfolge, die auf dem hart codierten Präfix () basiert, `"MinimumAge"` und eine ganze Zahl, die über den Konstruktor übergeben wird.

Sie können es auf die gleiche Weise wie andere `Authorize` Attribute anwenden, außer dass es eine ganze Zahl als Parameter annimmt.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Benutzerdefinierter iauthorizationpolicyprovider

Das benutzerdefinierte vereinfacht das `MinimumAgeAuthorizeAttribute` anfordern von Autorisierungs Richtlinien für jedes gewünschte minimal Alter. Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungs Richtlinien für alle unterschiedlichen Alters Bereiche verfügbar sind. An dieser Stelle `IAuthorizationPolicyProvider` ist ein nützlich.

Bei Verwendung `MinimumAgeAuthorizationAttribute` von folgen die Namen der Autorisierungs Richtlinien dem Muster `"MinimumAge" + Age` , sodass die benutzerdefinierte `IAuthorizationPolicyProvider` Autorisierungs Richtlinien generieren soll:

* Das Alter wird anhand des Richtlinien namens verarbeitet.
* Verwenden `AuthorizationPolicyBuilder` von zum Erstellen eines neuen `AuthorizationPolicy`
* In diesem und den folgenden Beispielen wird davon ausgegangen, dass der Benutzer über eine authentifiziert wird cookie . `AuthorizationPolicyBuilder`Muss entweder mit mindestens einem Autorisierungs Schema Namen erstellt werden oder immer erfolgreich sein. Andernfalls gibt es keine Informationen zum Bereitstellen einer Herausforderung für den Benutzer, und es wird eine Ausnahme ausgelöst.
* Hinzufügen von Anforderungen zur Richtlinie basierend auf dem Alter mit `AuthorizationPolicyBuilder.AddRequirements` . In anderen Szenarien können Sie `RequireClaim` `RequireRole` stattdessen, oder verwenden `RequireUserName` .

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Mehrere Autorisierungs Richtlinien Anbieter

Beachten Sie bei der Verwendung von benutzerdefinierten `IAuthorizationPolicyProvider` Implementierungen, dass ASP.net Core nur eine Instanz von verwendet `IAuthorizationPolicyProvider` . Wenn ein benutzerdefinierter Anbieter keine Autorisierungs Richtlinien für alle zu verwendenden Richtlinien Namen bereitstellen kann, sollte er an einen Sicherungs Anbieter zurückgestellt werden. 

Stellen Sie sich beispielsweise eine Anwendung vor, die sowohl benutzerdefinierte Alters Richtlinien als auch herkömmlicheren rollenbasierten Richtlinien Abruf benötigt. Eine solche APP könnte einen benutzerdefinierten Autorisierungs Richtlinien Anbieter verwenden, der Folgendes:

* Versucht, Richtlinien Namen zu analysieren. 
* Ruft einen anderen Richtlinien Anbieter auf (z `DefaultAuthorizationPolicyProvider` . b.), wenn der Richtlinien Name kein Alter enthält.

Die `IAuthorizationPolicyProvider` oben gezeigte Beispiel Implementierung kann aktualisiert werden, um die zu verwenden, `DefaultAuthorizationPolicyProvider` indem ein Sicherungs Richtlinien Anbieter im Konstruktor erstellt wird (für den Fall, dass der Richtlinien Name nicht mit dem erwarteten Muster von "minimumAge" + Age identisch ist).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Anschließend kann die- `GetPolicyAsync` Methode so aktualisiert werden, dass `BackupPolicyProvider` anstelle von NULL verwendet wird:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Standardrichtlinie

Zusätzlich zur Bereitstellung benannter Autorisierungs Richtlinien muss ein benutzerdefiniertes `IAuthorizationPolicyProvider` implementieren, `GetDefaultPolicyAsync` um eine Autorisierungs Richtlinie für Attribute bereitzustellen, `[Authorize]` ohne dass ein Richtlinien Name angegeben wird.

In vielen Fällen erfordert dieses Autorisierungs Attribut nur einen authentifizierten Benutzer, sodass Sie die erforderliche Richtlinie mit einem-Befehl erstellen können `RequireAuthenticatedUser` :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Wie bei allen Aspekten eines benutzerdefinierten `IAuthorizationPolicyProvider` können Sie dies nach Bedarf anpassen. In einigen Fällen kann es wünschenswert sein, die Standard Richtlinie von einem Fall Back abzurufen `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>Fallback-Richtlinie

Ein benutzerdefiniertes `IAuthorizationPolicyProvider` kann optional implementieren `GetFallbackPolicyAsync` , um eine Richtlinie bereitzustellen, die beim [Kombinieren von Richtlinien](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) verwendet wird, und wenn keine Richtlinien Wenn `GetFallbackPolicyAsync` eine Richtlinie ungleich NULL zurückgibt, wird die zurückgegebene Richtlinie von der Autorisierungs Middleware verwendet, wenn keine Richtlinien für die Anforderung angegeben werden.

Wenn keine Fall Back Richtlinie erforderlich ist, kann der Anbieter `null` den Fall Back Anbieter zurückgeben oder Zurückstellen:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Verwenden eines benutzerdefinierten iauthorizationpolicyprovider

Wenn Sie benutzerdefinierte Richtlinien aus einer verwenden möchten `IAuthorizationPolicyProvider` , **müssen** Sie die folgenden Schritte ausführen:

_ Registrieren Sie die geeigneten `AuthorizationHandler` Typen mit Abhängigkeitsinjektion (in [Richtlinien basierter Autorisierung](xref:security/authorization/policies#authorization-handlers)beschrieben), wie bei allen Richtlinien basierten Autorisierungs Szenarios.
* Registrieren Sie den benutzerdefinierten `IAuthorizationPolicyProvider` Typ in der Abhängigkeitsinjektion-Dienst Auflistung von der app in `Startup.ConfigureServices` , um den Standardrichtlinien Anbieter zu ersetzen.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

Eine komplette benutzerdefinierte `IAuthorizationPolicyProvider` Stichprobe finden Sie im [GitHub-Repository dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).
