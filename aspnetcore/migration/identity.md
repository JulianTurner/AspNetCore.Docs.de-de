---
title: Migrieren von Authentifizierung und Identity zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie die Authentifizierung und Identität von einem ASP.NET MVC-Projekt zu einem ASP.net Core MVC-Projekt migrieren.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060650"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>Migrieren von Authentifizierung und Identity zu ASP.net Core

Von [Steve Smith](https://ardalis.com/)

Im vorherigen Artikel haben wir die [Konfiguration von einem ASP.NET MVC-Projekt zu ASP.net Core MVC migriert](xref:migration/configuration). In diesem Artikel migrieren wir die Funktionen "Registrierung", "Anmeldung" und "Benutzerverwaltung".

## <a name="configure-no-locidentity-and-membership"></a>Konfigurieren Identity und Mitgliedschaft

In ASP.NET MVC werden Authentifizierungs-und Identitäts Funktionen mithilfe von ASP.net Identity in *Startup.auth.cs* und *Identity config.cs* konfiguriert, die sich im Ordner *App_Start* befinden. In ASP.net Core MVC sind diese Funktionen in *Startup.cs* konfiguriert.

Installieren Sie die folgenden NuGet-Pakete:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

Aktualisieren Sie in *Startup.cs* die `Startup.ConfigureServices` -Methode, um Entity Framework und Dienste zu verwenden Identity :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

An diesem Punkt gibt es zwei Typen, auf die im obigen Code verwiesen wird, die noch nicht aus dem ASP.NET MVC-Projekt migriert wurden: `ApplicationDbContext` und `ApplicationUser` . Erstellen Sie im ASP.net Core-Projekt einen neuen *Modell* Ordner, und fügen Sie ihm zwei Klassen entsprechend diesen Typen hinzu. Sie finden die ASP.NET MVC-Versionen dieser Klassen in */Models/ Identity Models.cs* , aber wir verwenden eine Datei pro Klasse im migrierten Projekt, da dies noch deutlicher ist.

*ApplicationUser.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Das ASP.net Core MVC-Starter-Webprojekt enthält keine große Anpassung an Benutzer oder `ApplicationDbContext` . Beim Migrieren einer echten App müssen Sie auch alle benutzerdefinierten Eigenschaften und Methoden des Benutzers und der Klassen der APP sowie alle `DbContext` anderen Modellklassen, die Ihre APP verwendet, migrieren. Wenn Ihr beispielsweise `DbContext` über eine verfügt `DbSet<Album>` , müssen Sie die-Klasse migrieren `Album` .

Wenn diese Dateien vorhanden sind, kann die *Startup.cs* -Datei zum Kompilieren durch Aktualisieren der zugehörigen-Anweisungen erstellt werden `using` :

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Unsere APP ist jetzt bereit, um die Authentifizierung und Dienste zu unterstützen Identity . Diese Features müssen nur Benutzern zur Verfügung gestellt werden.

## <a name="migrate-registration-and-login-logic"></a>Migrieren von Registrierungs-und Anmelde Logik

IdentityWenn für die APP konfigurierte Dienste und der Datenzugriff mit Entity Framework und SQL Server konfiguriert wurden, können wir die Unterstützung für die Registrierung und Anmeldung bei der APP hinzufügen. Beachten Sie, dass wir [zuvor im Migrationsprozess](xref:migration/mvc#migrate-the-layout-file) einen Verweis auf *_LoginPartial* in *_Layout. cshtml* auskommentiert haben. Nun ist es an der Zeit, zu diesem Code zurückzukehren, ihn zu entfernen und die erforderlichen Controller und Sichten hinzuzufügen, um die Anmelde Funktionalität zu unterstützen.

Entfernen Sie die Auskommentierung der `@Html.Partial` Zeile in *_Layout. cshtml* :

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Fügen Sie nun eine neue Razor Ansicht namens " *_LoginPartial* " zum Ordner " *views/Shared* " hinzu:

Aktualisieren Sie *_LoginPartial. cshtml* mit folgendem Code (ersetzen Sie den gesamten Inhalt):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

An diesem Punkt sollten Sie in der Lage sein, die Website in Ihrem Browser zu aktualisieren.

## <a name="summary"></a>Zusammenfassung

ASP.net Core führt Änderungen an den ASP.net- Identity Features ein. In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierungs-und Benutzer Verwaltungsfunktionen von ASP.net Identity zu ASP.net Core migrieren.
