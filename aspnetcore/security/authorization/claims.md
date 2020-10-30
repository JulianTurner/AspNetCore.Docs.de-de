---
title: Anspruchs basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Anspruchs Überprüfungen zur Autorisierung in einer ASP.net Core-app hinzufügen.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060273"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>Anspruchs basierte Autorisierung in ASP.net Core

<a name="security-authorization-claims-based"></a>

Wenn eine Identität erstellt wird, kann Ihr eine oder mehrere Ansprüche zugewiesen werden, die von einer vertrauenswürdigen Partei ausgegeben werden. Ein Anspruch ist ein Name-Wert-Paar, das den Betreff darstellt, nicht das, was der Betreff tun kann. Beispielsweise können Sie über die Lizenz eines Treibers verfügen, die von einer lokalen Führerschein Zertifizierungsstelle ausgestellt wurde. Die Lizenz Ihres Treibers hat das Geburtsdatum. In diesem Fall würde der Anspruchs Name lauten `DateOfBirth` . der Anspruchs Wert wäre z. b. das Geburtsdatum, `8th June 1970` und der Aussteller wäre die treibende Lizenz. Die Anspruchs basierte Autorisierung überprüft am einfachsten den Wert eines Anspruchs und ermöglicht den Zugriff auf eine Ressource basierend auf diesem Wert. Wenn Sie z. b. auf einen Nacht Club zugreifen möchten, könnte der Autorisierungs Prozess wie folgt lauten:

Der Door Security Officer wertet den Wert Ihres Geburts Geburts Anspruchs aus und gibt an, ob er dem Aussteller (der führerscheinbehörde) vertraut, bevor Sie Zugriff gewähren.

Eine Identität kann mehrere Ansprüche mit mehreren Werten enthalten und mehrere Ansprüche desselben Typs enthalten.

## <a name="adding-claims-checks"></a>Hinzufügen von Anspruchs Prüfungen

Anspruchs basierte Autorisierungs Überprüfungen sind deklarativ. der Entwickler bettet diese innerhalb Ihres Codes, für einen Controller oder eine Aktion innerhalb eines Controllers ein, gibt Ansprüche an, die der aktuelle Benutzer besitzen muss, und optional den Wert, den der Anspruch enthalten muss, um auf die angeforderte Ressource zuzugreifen. Anspruchs Anforderungen sind Richtlinien basiert. der Entwickler muss eine Richtlinie erstellen und registrieren, die die Anspruchs Anforderungen ausdrückt.

Der einfachste Typ der Anspruchs Richtlinie sucht nach dem vorhanden sein eines Anspruchs und überprüft den Wert nicht.

Zuerst müssen Sie die Richtlinie erstellen und registrieren. Dies erfolgt im Rahmen der Autorisierungs Dienst Konfiguration, die normalerweise in `ConfigureServices()` Ihrer *Startup.cs* -Datei teilnimmt.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

In diesem Fall `EmployeeOnly` prüft die Richtlinie, ob ein `EmployeeNumber` Anspruch auf die aktuelle Identität vorhanden ist.

Anschließend wenden Sie die Richtlinie mithilfe der- `Policy` Eigenschaft für das- `AuthorizeAttribute` Attribut an, um den Richtlinien Namen anzugeben.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

Das- `AuthorizeAttribute` Attribut kann auf einen gesamten Controller angewendet werden. in diesem Fall erhalten nur Identitäten, die mit der Richtlinie übereinstimmen, Zugriff auf jede Aktion auf dem Controller.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Wenn Sie über einen Controller verfügen, der durch das-Attribut geschützt ist `AuthorizeAttribute` , Sie jedoch den anonymen Zugriff auf bestimmte Aktionen zulassen möchten, wenden Sie das- `AllowAnonymousAttribute` Attribut an.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

Die meisten Ansprüche verfügen über einen Wert. Sie können eine Liste zulässiger Werte angeben, wenn Sie die Richtlinie erstellen. Das folgende Beispiel ist nur für Mitarbeiter erfolgreich, deren Mitarbeiternummer 1, 2, 3, 4 oder 5 war.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a>Generische Anspruchs Prüfung hinzufügen

Wenn der Anspruchs Wert kein einzelner Wert ist oder eine Transformation erforderlich ist, verwenden Sie [requireassertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Weitere Informationen finden Sie unter [Verwenden eines Func zum erfüllen einer Richtlinie](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Bewertung mehrerer Richtlinien

Wenn Sie mehrere Richtlinien auf einen Controller oder eine Aktion anwenden, müssen alle Richtlinien bestanden werden, bevor der Zugriff gewährt wird. Zum Beispiel:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

Im obigen Beispiel kann jede Identität, die die `EmployeeOnly` Richtlinie erfüllt, `Payslip` auf die Aktion zugreifen, da diese Richtlinie auf dem Controller erzwungen wird. Um die Aktion aufzurufen, `UpdateSalary` muss die Identität jedoch *sowohl* die `EmployeeOnly` Richtlinie als auch die `HumanResources` Richtlinie erfüllen.

Wenn Sie kompliziertere Richtlinien erstellen möchten, z. b. das Erstellen eines Geburts Geburts Anspruchs, das Berechnen eines Alters aus diesem und das überprüfen, ob das Alter 21 oder älter ist, müssen Sie [benutzerdefinierte Richtlinien Handler](xref:security/authorization/policies)schreiben.
