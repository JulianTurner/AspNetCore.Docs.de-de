---
title: Identity Modellanpassung in ASP.net Core
author: ajcvickers
description: In diesem Artikel wird beschrieben, wie das zugrunde liegende Entity Framework Core Datenmodell für angepasst wird ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
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
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052044"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a>Identity Modellanpassung in ASP.net Core

Von [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity stellt ein Framework zum Verwalten und Speichern von Benutzerkonten in ASP.net Core-apps bereit. Identity wird Ihrem Projekt hinzugefügt, wenn **einzelne Benutzerkonten** als Authentifizierungsmechanismus ausgewählt werden. Standardmäßig Identity verwendet ein Entity Framework Core-Datenmodell (EF). In diesem Artikel wird beschrieben, wie Sie das Identity Modell anpassen.

## <a name="no-locidentity-and-ef-core-migrations"></a>Identity und EF Core Migrationen

Bevor Sie das Modell untersuchen, sollten Sie verstehen, wie Identity mit [EF Core Migrationen](/ef/core/managing-schemas/migrations/) funktioniert, um eine Datenbank zu erstellen und zu aktualisieren. Auf der obersten Ebene lautet der Prozess wie folgt:

1. Definieren oder aktualisieren Sie ein [Datenmodell im Code](/ef/core/modeling/).
1. Fügen Sie eine Migration hinzu, um dieses Modell in Änderungen zu übersetzen, die auf die Datenbank angewendet werden können.
1. Überprüfen Sie, ob die Migration ordnungsgemäß ihren Absichten entspricht.
1. Wenden Sie die Migration an, um die Synchronisierung der Datenbank mit dem Modell zu aktualisieren.
1. Wiederholen Sie die Schritte 1 bis 4, um das Modell weiter zu verfeinern und die Datenbank synchron zu halten.

Verwenden Sie einen der folgenden Ansätze, um Migrationen hinzuzufügen und anzuwenden:

* Das Fenster der **Paket-Manager-Konsole** (PMC), wenn Sie Visual Studio verwenden. Weitere Informationen finden Sie unter [EF Core PMC-Tools](/ef/core/miscellaneous/cli/powershell).
* Der .net Core-CLI, wenn die Befehlszeile verwendet wird. Weitere Informationen finden Sie unter [EF Core .net-Befehlszeilen Tools](/ef/core/miscellaneous/cli/dotnet).
* Wenn die app ausgeführt wird, klicken Sie auf der Fehlerseite auf die Schaltfläche **Migrations anwenden** .

ASP.net Core hat einen Entwicklungszeit-Fehlerseiten Handler. Der Handler kann Migrationen anwenden, wenn die app ausgeführt wird. Produktionsanwendungen generieren in der Regel SQL-Skripts aus den Migrationen und stellen Daten Bank Änderungen im Rahmen einer kontrollierten APP-und Daten Bank Bereitstellung bereit.

Wenn eine neue APP mit Identity erstellt wird, sind die obigen Schritte 1 und 2 bereits abgeschlossen. Das heißt, das anfängliche Datenmodell ist bereits vorhanden, und die anfängliche Migration wurde dem Projekt hinzugefügt. Die anfängliche Migration muss noch auf die Datenbank angewendet werden. Die anfängliche Migration kann über einen der folgenden Ansätze angewendet werden:

* Führen Sie `Update-Database` in der PMC aus.
* Führen Sie `dotnet ef database update` in einer Befehlsshell aus.
* Wenn die app ausgeführt wird, klicken Sie auf der Fehlerseite auf die Schaltfläche **Migrations anwenden** .

Wiederholen Sie die vorherigen Schritte, wenn Änderungen am Modell vorgenommen werden.

## <a name="the-no-locidentity-model"></a>Das Identity Modell

### <a name="entity-types"></a>Entitätstypen

Das Identity Modell besteht aus den folgenden Entitäts Typen.

|Entitätstyp|Beschreibung                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Stellt den Benutzer dar.                                         |
|`Role`     |Stellt eine Rolle dar.                                           |
|`UserClaim`|Stellt einen Anspruch dar, den ein Benutzer besitzt.                    |
|`UserToken`|Stellt ein Authentifizierungs Token für einen Benutzer dar.               |
|`UserLogin`|Ordnet einem Benutzer einen Anmelde Namen zu.                              |
|`RoleClaim`|Stellt einen Anspruch dar, der allen Benutzern in einer Rolle gewährt wird.|
|`UserRole` |Eine JOIN-Entität, die Benutzer und Rollen zuordnet.               |

### <a name="entity-type-relationships"></a>Entitätstyp Beziehungen

Die [Entitäts Typen](#entity-types) stehen auf folgende Weise miteinander in Beziehung:

* Jeder `User` kann über viele verfügen `UserClaims` .
* Jeder `User` kann über viele verfügen `UserLogins` .
* Jeder `User` kann über viele verfügen `UserTokens` .
* Jedem `Role` können viele zugeordnet sein `RoleClaims` .
* Jeder `User` kann über viele zugeordnete verfügen `Roles` , und jeder `Role` kann mit vielen verknüpft werden `Users` . Dabei handelt es sich um eine m:n-Beziehung, die eine jointabelle in der Datenbank erfordert. Die jointabelle wird durch die- `UserRole` Entität dargestellt.

### <a name="default-model-configuration"></a>Standardmodell Konfiguration

Identity definiert viele *Kontext Klassen* , die von [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) erben, um das Modell zu konfigurieren und zu verwenden. Diese Konfiguration erfolgt mithilfe der [EF Core Code First fließende API](/ef/core/modeling/) in der [onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) -Methode der Context-Klasse. Die Standardkonfiguration lautet wie folgt:

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>Generische Modelltypen

Identity definiert CLR-Standardtypen ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) für jeden der oben aufgeführten Entitäts Typen. Diese Typen verfügen alle über das Präfix *Identity* :

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Anstatt diese Typen direkt zu verwenden, können die Typen als Basisklassen für die eigenen Typen der APP verwendet werden. Die `DbContext` von definierten Klassen Identity sind generisch, sodass verschiedene CLR-Typen für einen oder mehrere Entitäts Typen im Modell verwendet werden können. Diese generischen Typen ermöglichen auch das `User` Ändern des Primärschlüssel Datentyps (PK).

Wenn Sie Identity mit der Unterstützung für-Rollen verwenden, sollte eine- <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> Klasse verwendet werden. Zum Beispiel:

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

Es ist auch möglich, Identity ohne Rollen (nur Ansprüche) zu verwenden. in diesem Fall <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> sollte eine-Klasse verwendet werden:

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>Anpassen des Modells

Der Ausgangspunkt für die Modellanpassung ist die Ableitung vom entsprechenden Kontexttyp. Weitere Informationen finden Sie im Abschnitt [Modell generische Typen](#model-generic-types) . Dieser Kontexttyp wird in `ApplicationDbContext` der Regel aufgerufen und durch die ASP.net Core Vorlagen erstellt.

Der Kontext wird verwendet, um das Modell auf zwei Arten zu konfigurieren:

* Bereitstellen von Entitäts-und Schlüsseltypen für die generischen Typparameter.
* `OnModelCreating`Überschreiben, um die Zuordnung dieser Typen zu ändern.

Beim Überschreiben muss `OnModelCreating` `base.OnModelCreating` zuerst aufgerufen werden. die über schreibende Konfiguration sollte als nächstes aufgerufen werden. EF Core in der Regel über eine Richtlinie mit dem letzten WINS für die Konfiguration verfügen. Wenn beispielsweise die `ToTable` -Methode für einen Entitätstyp zuerst mit einem Tabellennamen und später erneut mit einem anderen Tabellennamen aufgerufen wird, wird der Tabellenname im zweiten Aufruf verwendet.

### <a name="custom-user-data"></a>Benutzerdefinierte Benutzerdaten

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

Benutzer [definierte Benutzerdaten](xref:security/authentication/add-user-data) werden durch die Vererbung von unterstützt `IdentityUser` . Es ist üblich, diesen Typ zu benennen `ApplicationUser` :

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Verwenden Sie den- `ApplicationUser` Typ als generisches Argument für den Kontext:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

Es ist nicht erforderlich, in der-Klasse zu überschreiben `OnModelCreating` `ApplicationDbContext` . EF Core ordnet die `CustomTag` Eigenschaft nach Konvention zu. Die Datenbank muss jedoch aktualisiert werden, damit eine neue Spalte erstellt werden kann `CustomTag` . Um die Spalte zu erstellen, fügen Sie eine Migration hinzu, und aktualisieren Sie dann die Datenbank, wie in [ Identity und EF Core Migrationen](#identity-and-ef-core-migrations)beschrieben.

Aktualisieren Sie *pages/Shared/_LoginPartial. cshtml* , und ersetzen Sie `IdentityUser` durch `ApplicationUser` :

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Aktualisieren Sie *Bereiche/ Identity / Identity HostingStartup.cs* oder, `Startup.ConfigureServices` und ersetzen Sie `IdentityUser` durch `ApplicationUser` .

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` . Weitere Informationen finden Sie unter

* [Gerüst Identity](xref:security/authentication/scaffold-identity)
* [Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten Identity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Ändern des Primärschlüssel Typs

Eine Änderung des Datentyps der PK-Spalte, nachdem die Datenbank erstellt wurde, ist für viele Datenbanksysteme problematisch. Das Ändern des PK umfasst in der Regel das Löschen und erneute Erstellen der Tabelle. Daher sollten bei der ersten Migration beim Erstellen der Datenbank Schlüsseltypen angegeben werden.

Führen Sie diese Schritte aus, um den PK-Typ zu ändern:

1. Wenn die Datenbank vor der PK-Änderung erstellt wurde, führen `Drop-Database` Sie (PMC) oder `dotnet ef database drop` (.net Core-CLI) aus, um Sie zu löschen.
2. Nachdem Sie das Löschen der Datenbank bestätigt haben, entfernen Sie die anfängliche Migration mit `Remove-Migration` (PMC) oder `dotnet ef migrations remove` (.net Core-CLI).
3. Aktualisieren Sie die- `ApplicationDbContext` Klasse, um von abgeleitet zu werden <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> . Geben Sie den neuen Schlüsseltyp für an `TKey` . Verwenden Sie beispielsweise, um einen `Guid` Schlüsseltyp zu verwenden:

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    Im vorangehenden Code müssen die generischen Klassen <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> angegeben werden, um den neuen Schlüsseltyp zu verwenden.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    Im vorangehenden Code müssen die generischen Klassen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> angegeben werden, um den neuen Schlüsseltyp zu verwenden.

    ::: moniker-end

    `Startup.ConfigureServices` muss aktualisiert werden, damit der generische Benutzer verwendet wird:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. Wenn eine benutzerdefinierte `ApplicationUser` Klasse verwendet wird, aktualisieren Sie die-Klasse, um von zu erben `IdentityUser` . Zum Beispiel:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Aktualisieren `ApplicationDbContext` Sie, um auf die benutzerdefinierte Klasse zu verweisen `ApplicationUser` :

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.

    In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert einen `TKey` Typ, der den Datentyp des Primärschlüssels angibt.

    ::: moniker-end

5. Wenn eine benutzerdefinierte `ApplicationRole` Klasse verwendet wird, aktualisieren Sie die-Klasse, um von zu erben `IdentityRole<TKey>` . Zum Beispiel:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Aktualisieren `ApplicationDbContext` Sie, um auf die benutzerdefinierte Klasse zu verweisen `ApplicationRole` . Die folgende Klasse verweist z. b. auf ein benutzerdefiniertes `ApplicationUser` und ein benutzerdefiniertes `ApplicationRole` :

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.

    In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt. Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>. Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert einen `TKey` Typ, der den Datentyp des Primärschlüssels angibt.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Navigations Eigenschaften hinzufügen

Das Ändern der Modell Konfiguration für Beziehungen kann schwieriger sein als das vornehmen anderer Änderungen. Es muss darauf geachtet werden, dass vorhandene Beziehungen ersetzt werden, anstatt neue, zusätzliche Beziehungen zu erstellen. Insbesondere muss die geänderte Beziehung dieselbe Fremdschlüssel Eigenschaft (FK) wie die vorhandene Beziehung angeben. Beispielsweise wird die Beziehung zwischen `Users` und `UserClaims` standardmäßig wie folgt angegeben:

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

Der FK für diese Beziehung wird als- `UserClaim.UserId` Eigenschaft angegeben. `HasMany` und `WithOne` werden ohne Argumente aufgerufen, um die Beziehung ohne Navigations Eigenschaften zu erstellen.

Fügen Sie eine Navigations Eigenschaft hinzu `ApplicationUser` , die `UserClaims` den Verweis auf den zugeordneten vom Benutzer ermöglicht:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

Der `TKey` für `IdentityUserClaim<TKey>` ist der Typ, der für den PK der Benutzer angegeben ist. In diesem Fall `TKey` ist, `string` weil die Standardwerte verwendet werden. Es ist **nicht** der PK-Typ für den `UserClaim` Entitätstyp.

Nun, da die Navigations Eigenschaft vorhanden ist, muss Sie in konfiguriert werden `OnModelCreating` :

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

Beachten Sie, dass die Beziehung genau so konfiguriert ist, wie Sie zuvor war, nur mit einer Navigations Eigenschaft, die im-Befehl angegeben wurde `HasMany` .

Die Navigations Eigenschaften sind nur im EF-Modell vorhanden, nicht in der Datenbank. Da sich der FK für die Beziehung nicht geändert hat, ist für diese Art von Modell Änderung keine Aktualisierung der Datenbank erforderlich. Dies kann durch Hinzufügen einer Migration nach dem vornehmen von Änderungen geprüft werden. Die `Up` -Methode und die- `Down` Methode sind leer.

### <a name="add-all-user-navigation-properties"></a>Alle Benutzer Navigations Eigenschaften hinzufügen

Im folgenden Beispiel werden die unidirektionalen Navigations Eigenschaften für alle Beziehungen für den Benutzer mithilfe des obigen Abschnitts konfiguriert:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>Benutzer-und Rollen Navigations Eigenschaften hinzufügen

Im folgenden Beispiel werden die Navigations Eigenschaften für alle Beziehungen von Benutzer und Rolle mithilfe des obigen Abschnitts konfiguriert:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

Hinweise:

* Dieses Beispiel enthält auch die `UserRole` Join-Entität, die benötigt wird, um die m:n-Beziehung von Benutzern zu Rollen zu navigieren.
* Denken Sie daran, die Typen der Navigations Eigenschaften zu ändern, um widerzuspiegeln, dass `Application{...}` Typen jetzt anstelle von-Typen verwendet werden `Identity{...}` .
* Denken Sie daran, das `Application{...}` in der generischen Definition zu verwenden `ApplicationContext` .

### <a name="add-all-navigation-properties"></a>Alle Navigations Eigenschaften hinzufügen

Im folgenden Beispiel werden die Navigations Eigenschaften für alle Beziehungen für alle Entitäts Typen mithilfe des obigen Abschnitts konfiguriert:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>Verwenden von zusammengesetzten Schlüsseln

In den vorherigen Abschnitten wurde veranschaulicht, wie Sie den im Modell verwendeten Schlüsseltyp ändern Identity . Das Ändern des Identity Schlüssel Modells zur Verwendung von zusammengesetzten Schlüsseln wird nicht unterstützt oder empfohlen. Die Verwendung eines zusammengesetzten Schlüssels mit Identity umfasst das Ändern der Identity interagiert des Manager-Codes mit dem Modell. Diese Anpassung geht über den Rahmen dieses Dokuments hinaus.

### <a name="change-tablecolumn-names-and-facets"></a>Ändern von Tabellen-/Spaltennamen und Facetten

Um die Namen von Tabellen und Spalten zu ändern, wird aufgerufen `base.OnModelCreating` . Fügen Sie dann die Konfiguration hinzu, um die Standardwerte zu überschreiben. So ändern Sie z. b. den Namen aller Identity Tabellen:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

In diesen Beispielen werden die Standard Identity Typen verwendet. Wenn Sie einen app-Typ wie verwenden `ApplicationUser` , konfigurieren Sie diesen Typ anstelle des Standard Typs.

Im folgenden Beispiel werden einige Spaltennamen geändert:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

Einige Typen von Daten Bank Spalten können mit bestimmten *Facetten* konfiguriert werden (z. b. die maximal `string` zulässige Länge). Im folgenden Beispiel werden Spalten maximale Länge für mehrere `string` Eigenschaften im Modell festgelegt:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>Einem anderen Schema zuordnen

Schemas können sich über Datenbankanbieter hinweg unterschiedlich verhalten. Für SQL Server werden standardmäßig alle Tabellen im *dbo* -Schema erstellt. Die Tabellen können in einem anderen Schema erstellt werden. Zum Beispiel:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Lazy Loading

In diesem Abschnitt wird die Unterstützung für Lazy-Load-Proxys im Identity Modell hinzugefügt. Lazy-Load ist nützlich, da Navigations Eigenschaften verwendet werden können, ohne zuvor sicherzustellen, dass Sie geladen werden.

Entitäts Typen können auf verschiedene Weise für Lazy-Load geeignet gemacht werden, wie in der [EF Core-Dokumentation](/ef/core/querying/related-data#lazy-loading)beschrieben. Verwenden Sie zur Vereinfachung das verzögerte Laden von Proxys, für die Folgendes erforderlich ist:

* Installation des Pakets " [Microsoft. entityframeworkcore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) ".
* Ein Aufrufe von <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> in [adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Öffentliche Entitäts Typen mit `public virtual` Navigations Eigenschaften.

Im folgenden Beispiel wird das Aufrufen von `UseLazyLoadingProxies` in veranschaulicht `Startup.ConfigureServices` :

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

In den vorangehenden Beispielen finden Sie Anleitungen zum Hinzufügen von Navigations Eigenschaften zu den Entitäts Typen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authentication/scaffold-identity>

::: moniker-end
