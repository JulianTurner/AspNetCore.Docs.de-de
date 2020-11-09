---
title: 'Identity Modellanpassung in ASP.net Core'
author: ajcvickers
description: 'In diesem Artikel wird beschrieben, wie das zugrunde liegende Entity Framework Core Datenmodell für angepasst wird ASP.NET Core Identity .'
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 6e520c76a3377e889166ca8d08b75754ef34b6a1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052044"
---
# <a name="no-locidentity-model-customization-in-aspnet-core"></a><span data-ttu-id="4fd30-103">Identity Modellanpassung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4fd30-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="4fd30-104">Von [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="4fd30-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="4fd30-105">ASP.NET Core Identity stellt ein Framework zum Verwalten und Speichern von Benutzerkonten in ASP.net Core-apps bereit.</span><span class="sxs-lookup"><span data-stu-id="4fd30-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="4fd30-106">Identity wird Ihrem Projekt hinzugefügt, wenn **einzelne Benutzerkonten** als Authentifizierungsmechanismus ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="4fd30-107">Standardmäßig Identity verwendet ein Entity Framework Core-Datenmodell (EF).</span><span class="sxs-lookup"><span data-stu-id="4fd30-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="4fd30-108">In diesem Artikel wird beschrieben, wie Sie das Identity Modell anpassen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-108">This article describes how to customize the Identity model.</span></span>

## <a name="no-locidentity-and-ef-core-migrations"></a><span data-ttu-id="4fd30-109">Identity und EF Core Migrationen</span><span class="sxs-lookup"><span data-stu-id="4fd30-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="4fd30-110">Bevor Sie das Modell untersuchen, sollten Sie verstehen, wie Identity mit [EF Core Migrationen](/ef/core/managing-schemas/migrations/) funktioniert, um eine Datenbank zu erstellen und zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4fd30-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="4fd30-111">Auf der obersten Ebene lautet der Prozess wie folgt:</span><span class="sxs-lookup"><span data-stu-id="4fd30-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="4fd30-112">Definieren oder aktualisieren Sie ein [Datenmodell im Code](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="4fd30-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="4fd30-113">Fügen Sie eine Migration hinzu, um dieses Modell in Änderungen zu übersetzen, die auf die Datenbank angewendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4fd30-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="4fd30-114">Überprüfen Sie, ob die Migration ordnungsgemäß ihren Absichten entspricht.</span><span class="sxs-lookup"><span data-stu-id="4fd30-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="4fd30-115">Wenden Sie die Migration an, um die Synchronisierung der Datenbank mit dem Modell zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4fd30-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="4fd30-116">Wiederholen Sie die Schritte 1 bis 4, um das Modell weiter zu verfeinern und die Datenbank synchron zu halten.</span><span class="sxs-lookup"><span data-stu-id="4fd30-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="4fd30-117">Verwenden Sie einen der folgenden Ansätze, um Migrationen hinzuzufügen und anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="4fd30-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="4fd30-118">Das Fenster der **Paket-Manager-Konsole** (PMC), wenn Sie Visual Studio verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="4fd30-119">Weitere Informationen finden Sie unter [EF Core PMC-Tools](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="4fd30-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="4fd30-120">Der .net Core-CLI, wenn die Befehlszeile verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4fd30-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="4fd30-121">Weitere Informationen finden Sie unter [EF Core .net-Befehlszeilen Tools](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="4fd30-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="4fd30-122">Wenn die app ausgeführt wird, klicken Sie auf der Fehlerseite auf die Schaltfläche **Migrations anwenden** .</span><span class="sxs-lookup"><span data-stu-id="4fd30-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="4fd30-123">ASP.net Core hat einen Entwicklungszeit-Fehlerseiten Handler.</span><span class="sxs-lookup"><span data-stu-id="4fd30-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="4fd30-124">Der Handler kann Migrationen anwenden, wenn die app ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4fd30-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="4fd30-125">Produktionsanwendungen generieren in der Regel SQL-Skripts aus den Migrationen und stellen Daten Bank Änderungen im Rahmen einer kontrollierten APP-und Daten Bank Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="4fd30-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="4fd30-126">Wenn eine neue APP mit Identity erstellt wird, sind die obigen Schritte 1 und 2 bereits abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="4fd30-127">Das heißt, das anfängliche Datenmodell ist bereits vorhanden, und die anfängliche Migration wurde dem Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="4fd30-128">Die anfängliche Migration muss noch auf die Datenbank angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="4fd30-129">Die anfängliche Migration kann über einen der folgenden Ansätze angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="4fd30-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="4fd30-130">Führen Sie `Update-Database` in der PMC aus.</span><span class="sxs-lookup"><span data-stu-id="4fd30-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="4fd30-131">Führen Sie `dotnet ef database update` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="4fd30-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="4fd30-132">Wenn die app ausgeführt wird, klicken Sie auf der Fehlerseite auf die Schaltfläche **Migrations anwenden** .</span><span class="sxs-lookup"><span data-stu-id="4fd30-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="4fd30-133">Wiederholen Sie die vorherigen Schritte, wenn Änderungen am Modell vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-no-locidentity-model"></a><span data-ttu-id="4fd30-134">Das Identity Modell</span><span class="sxs-lookup"><span data-stu-id="4fd30-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="4fd30-135">Entitätstypen</span><span class="sxs-lookup"><span data-stu-id="4fd30-135">Entity types</span></span>

<span data-ttu-id="4fd30-136">Das Identity Modell besteht aus den folgenden Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="4fd30-137">Entitätstyp</span><span class="sxs-lookup"><span data-stu-id="4fd30-137">Entity type</span></span>|<span data-ttu-id="4fd30-138">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4fd30-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="4fd30-139">Stellt den Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="4fd30-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="4fd30-140">Stellt eine Rolle dar.</span><span class="sxs-lookup"><span data-stu-id="4fd30-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="4fd30-141">Stellt einen Anspruch dar, den ein Benutzer besitzt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="4fd30-142">Stellt ein Authentifizierungs Token für einen Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="4fd30-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="4fd30-143">Ordnet einem Benutzer einen Anmelde Namen zu.</span><span class="sxs-lookup"><span data-stu-id="4fd30-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="4fd30-144">Stellt einen Anspruch dar, der allen Benutzern in einer Rolle gewährt wird.</span><span class="sxs-lookup"><span data-stu-id="4fd30-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="4fd30-145">Eine JOIN-Entität, die Benutzer und Rollen zuordnet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="4fd30-146">Entitätstyp Beziehungen</span><span class="sxs-lookup"><span data-stu-id="4fd30-146">Entity type relationships</span></span>

<span data-ttu-id="4fd30-147">Die [Entitäts Typen](#entity-types) stehen auf folgende Weise miteinander in Beziehung:</span><span class="sxs-lookup"><span data-stu-id="4fd30-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="4fd30-148">Jeder `User` kann über viele verfügen `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="4fd30-149">Jeder `User` kann über viele verfügen `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="4fd30-150">Jeder `User` kann über viele verfügen `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="4fd30-151">Jedem `Role` können viele zugeordnet sein `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="4fd30-152">Jeder `User` kann über viele zugeordnete verfügen `Roles` , und jeder `Role` kann mit vielen verknüpft werden `Users` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="4fd30-153">Dabei handelt es sich um eine m:n-Beziehung, die eine jointabelle in der Datenbank erfordert.</span><span class="sxs-lookup"><span data-stu-id="4fd30-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="4fd30-154">Die jointabelle wird durch die- `UserRole` Entität dargestellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="4fd30-155">Standardmodell Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4fd30-155">Default model configuration</span></span>

<span data-ttu-id="4fd30-156">Identity definiert viele *Kontext Klassen* , die von [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) erben, um das Modell zu konfigurieren und zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="4fd30-157">Diese Konfiguration erfolgt mithilfe der [EF Core Code First fließende API](/ef/core/modeling/) in der [onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) -Methode der Context-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4fd30-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="4fd30-158">Die Standardkonfiguration lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="4fd30-158">The default configuration is:</span></span>

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

### <a name="model-generic-types"></a><span data-ttu-id="4fd30-159">Generische Modelltypen</span><span class="sxs-lookup"><span data-stu-id="4fd30-159">Model generic types</span></span>

<span data-ttu-id="4fd30-160">Identity definiert CLR-Standardtypen ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) für jeden der oben aufgeführten Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="4fd30-161">Diese Typen verfügen alle über das Präfix *Identity* :</span><span class="sxs-lookup"><span data-stu-id="4fd30-161">These types are all prefixed with *Identity* :</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="4fd30-162">Anstatt diese Typen direkt zu verwenden, können die Typen als Basisklassen für die eigenen Typen der APP verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="4fd30-163">Die `DbContext` von definierten Klassen Identity sind generisch, sodass verschiedene CLR-Typen für einen oder mehrere Entitäts Typen im Modell verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="4fd30-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="4fd30-164">Diese generischen Typen ermöglichen auch das `User` Ändern des Primärschlüssel Datentyps (PK).</span><span class="sxs-lookup"><span data-stu-id="4fd30-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="4fd30-165">Wenn Sie Identity mit der Unterstützung für-Rollen verwenden, sollte eine- <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> Klasse verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="4fd30-166">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4fd30-166">For example:</span></span>

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

<span data-ttu-id="4fd30-167">Es ist auch möglich, Identity ohne Rollen (nur Ansprüche) zu verwenden. in diesem Fall <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> sollte eine-Klasse verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="4fd30-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

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

## <a name="customize-the-model"></a><span data-ttu-id="4fd30-168">Anpassen des Modells</span><span class="sxs-lookup"><span data-stu-id="4fd30-168">Customize the model</span></span>

<span data-ttu-id="4fd30-169">Der Ausgangspunkt für die Modellanpassung ist die Ableitung vom entsprechenden Kontexttyp.</span><span class="sxs-lookup"><span data-stu-id="4fd30-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="4fd30-170">Weitere Informationen finden Sie im Abschnitt [Modell generische Typen](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="4fd30-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="4fd30-171">Dieser Kontexttyp wird in `ApplicationDbContext` der Regel aufgerufen und durch die ASP.net Core Vorlagen erstellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="4fd30-172">Der Kontext wird verwendet, um das Modell auf zwei Arten zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4fd30-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="4fd30-173">Bereitstellen von Entitäts-und Schlüsseltypen für die generischen Typparameter.</span><span class="sxs-lookup"><span data-stu-id="4fd30-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="4fd30-174">`OnModelCreating`Überschreiben, um die Zuordnung dieser Typen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="4fd30-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="4fd30-175">Beim Überschreiben muss `OnModelCreating` `base.OnModelCreating` zuerst aufgerufen werden. die über schreibende Konfiguration sollte als nächstes aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="4fd30-176">EF Core in der Regel über eine Richtlinie mit dem letzten WINS für die Konfiguration verfügen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="4fd30-177">Wenn beispielsweise die `ToTable` -Methode für einen Entitätstyp zuerst mit einem Tabellennamen und später erneut mit einem anderen Tabellennamen aufgerufen wird, wird der Tabellenname im zweiten Aufruf verwendet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="4fd30-178">Benutzerdefinierte Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="4fd30-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="4fd30-179">Benutzer [definierte Benutzerdaten](xref:security/authentication/add-user-data) werden durch die Vererbung von unterstützt `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="4fd30-180">Es ist üblich, diesen Typ zu benennen `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="4fd30-181">Verwenden Sie den- `ApplicationUser` Typ als generisches Argument für den Kontext:</span><span class="sxs-lookup"><span data-stu-id="4fd30-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

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

<span data-ttu-id="4fd30-182">Es ist nicht erforderlich, in der-Klasse zu überschreiben `OnModelCreating` `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="4fd30-183">EF Core ordnet die `CustomTag` Eigenschaft nach Konvention zu.</span><span class="sxs-lookup"><span data-stu-id="4fd30-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="4fd30-184">Die Datenbank muss jedoch aktualisiert werden, damit eine neue Spalte erstellt werden kann `CustomTag` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="4fd30-185">Um die Spalte zu erstellen, fügen Sie eine Migration hinzu, und aktualisieren Sie dann die Datenbank, wie in [ Identity und EF Core Migrationen](#identity-and-ef-core-migrations)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4fd30-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="4fd30-186">Aktualisieren Sie *pages/Shared/_LoginPartial. cshtml* , und ersetzen Sie `IdentityUser` durch `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="4fd30-187">Aktualisieren Sie *Bereiche/ Identity / Identity HostingStartup.cs* oder, `Startup.ConfigureServices` und ersetzen Sie `IdentityUser` durch `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="4fd30-188">In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="4fd30-189">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="4fd30-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="4fd30-190">Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="4fd30-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="4fd30-191">Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="4fd30-192">Weitere Informationen finden Sie unter</span><span class="sxs-lookup"><span data-stu-id="4fd30-192">For more information, see:</span></span>

* [<span data-ttu-id="4fd30-193">Gerüst Identity</span><span class="sxs-lookup"><span data-stu-id="4fd30-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="4fd30-194">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten Identity</span><span class="sxs-lookup"><span data-stu-id="4fd30-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="4fd30-195">Ändern des Primärschlüssel Typs</span><span class="sxs-lookup"><span data-stu-id="4fd30-195">Change the primary key type</span></span>

<span data-ttu-id="4fd30-196">Eine Änderung des Datentyps der PK-Spalte, nachdem die Datenbank erstellt wurde, ist für viele Datenbanksysteme problematisch.</span><span class="sxs-lookup"><span data-stu-id="4fd30-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="4fd30-197">Das Ändern des PK umfasst in der Regel das Löschen und erneute Erstellen der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="4fd30-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="4fd30-198">Daher sollten bei der ersten Migration beim Erstellen der Datenbank Schlüsseltypen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="4fd30-199">Führen Sie diese Schritte aus, um den PK-Typ zu ändern:</span><span class="sxs-lookup"><span data-stu-id="4fd30-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="4fd30-200">Wenn die Datenbank vor der PK-Änderung erstellt wurde, führen `Drop-Database` Sie (PMC) oder `dotnet ef database drop` (.net Core-CLI) aus, um Sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="4fd30-201">Nachdem Sie das Löschen der Datenbank bestätigt haben, entfernen Sie die anfängliche Migration mit `Remove-Migration` (PMC) oder `dotnet ef migrations remove` (.net Core-CLI).</span><span class="sxs-lookup"><span data-stu-id="4fd30-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="4fd30-202">Aktualisieren Sie die- `ApplicationDbContext` Klasse, um von abgeleitet zu werden <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="4fd30-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="4fd30-203">Geben Sie den neuen Schlüsseltyp für an `TKey` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="4fd30-204">Verwenden Sie beispielsweise, um einen `Guid` Schlüsseltyp zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4fd30-204">For example, to use a `Guid` key type:</span></span>

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

    <span data-ttu-id="4fd30-205">Im vorangehenden Code müssen die generischen Klassen <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> angegeben werden, um den neuen Schlüsseltyp zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="4fd30-206">Im vorangehenden Code müssen die generischen Klassen <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> und <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> angegeben werden, um den neuen Schlüsseltyp zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="4fd30-207">`Startup.ConfigureServices` muss aktualisiert werden, damit der generische Benutzer verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4fd30-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

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

4. <span data-ttu-id="4fd30-208">Wenn eine benutzerdefinierte `ApplicationUser` Klasse verwendet wird, aktualisieren Sie die-Klasse, um von zu erben `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="4fd30-209">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4fd30-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="4fd30-210">Aktualisieren `ApplicationDbContext` Sie, um auf die benutzerdefinierte Klasse zu verweisen `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

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

    <span data-ttu-id="4fd30-211">Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="4fd30-212">Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="4fd30-213">In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="4fd30-214">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="4fd30-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="4fd30-215">Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="4fd30-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="4fd30-216">Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="4fd30-217">Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="4fd30-218">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert einen `TKey` Typ, der den Datentyp des Primärschlüssels angibt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="4fd30-219">Wenn eine benutzerdefinierte `ApplicationRole` Klasse verwendet wird, aktualisieren Sie die-Klasse, um von zu erben `IdentityRole<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="4fd30-220">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4fd30-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="4fd30-221">Aktualisieren `ApplicationDbContext` Sie, um auf die benutzerdefinierte Klasse zu verweisen `ApplicationRole` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="4fd30-222">Die folgende Klasse verweist z. b. auf ein benutzerdefiniertes `ApplicationUser` und ein benutzerdefiniertes `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="4fd30-223">Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="4fd30-224">Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="4fd30-225">In ASP.net Core 2,1 oder höher Identity wird als Razor Klassenbibliothek bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="4fd30-226">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="4fd30-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="4fd30-227">Folglich erfordert der vorangehende Code einen-Rückruf <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="4fd30-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="4fd30-228">Wenn das Identity Gerüst zum Hinzufügen von Identity Dateien zum Projekt verwendet wurde, entfernen Sie den-Befehl `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="4fd30-229">Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="4fd30-230">Der Datentyp des Primärschlüssels wird durch Analysieren des [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) -Objekts abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="4fd30-231">Registrieren Sie die benutzerdefinierte Daten Bank Kontext Klasse, wenn Sie den Identity Dienst in hinzufügen `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="4fd30-232">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> -Methode akzeptiert einen `TKey` Typ, der den Datentyp des Primärschlüssels angibt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="4fd30-233">Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4fd30-233">Add navigation properties</span></span>

<span data-ttu-id="4fd30-234">Das Ändern der Modell Konfiguration für Beziehungen kann schwieriger sein als das vornehmen anderer Änderungen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="4fd30-235">Es muss darauf geachtet werden, dass vorhandene Beziehungen ersetzt werden, anstatt neue, zusätzliche Beziehungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="4fd30-236">Insbesondere muss die geänderte Beziehung dieselbe Fremdschlüssel Eigenschaft (FK) wie die vorhandene Beziehung angeben.</span><span class="sxs-lookup"><span data-stu-id="4fd30-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="4fd30-237">Beispielsweise wird die Beziehung zwischen `Users` und `UserClaims` standardmäßig wie folgt angegeben:</span><span class="sxs-lookup"><span data-stu-id="4fd30-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

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

<span data-ttu-id="4fd30-238">Der FK für diese Beziehung wird als- `UserClaim.UserId` Eigenschaft angegeben.</span><span class="sxs-lookup"><span data-stu-id="4fd30-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="4fd30-239">`HasMany` und `WithOne` werden ohne Argumente aufgerufen, um die Beziehung ohne Navigations Eigenschaften zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="4fd30-240">Fügen Sie eine Navigations Eigenschaft hinzu `ApplicationUser` , die `UserClaims` den Verweis auf den zugeordneten vom Benutzer ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="4fd30-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="4fd30-241">Der `TKey` für `IdentityUserClaim<TKey>` ist der Typ, der für den PK der Benutzer angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="4fd30-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="4fd30-242">In diesem Fall `TKey` ist, `string` weil die Standardwerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="4fd30-243">Es ist **nicht** der PK-Typ für den `UserClaim` Entitätstyp.</span><span class="sxs-lookup"><span data-stu-id="4fd30-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="4fd30-244">Nun, da die Navigations Eigenschaft vorhanden ist, muss Sie in konfiguriert werden `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

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

<span data-ttu-id="4fd30-245">Beachten Sie, dass die Beziehung genau so konfiguriert ist, wie Sie zuvor war, nur mit einer Navigations Eigenschaft, die im-Befehl angegeben wurde `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="4fd30-246">Die Navigations Eigenschaften sind nur im EF-Modell vorhanden, nicht in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4fd30-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="4fd30-247">Da sich der FK für die Beziehung nicht geändert hat, ist für diese Art von Modell Änderung keine Aktualisierung der Datenbank erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4fd30-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="4fd30-248">Dies kann durch Hinzufügen einer Migration nach dem vornehmen von Änderungen geprüft werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="4fd30-249">Die `Up` -Methode und die- `Down` Methode sind leer.</span><span class="sxs-lookup"><span data-stu-id="4fd30-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="4fd30-250">Alle Benutzer Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4fd30-250">Add all User navigation properties</span></span>

<span data-ttu-id="4fd30-251">Im folgenden Beispiel werden die unidirektionalen Navigations Eigenschaften für alle Beziehungen für den Benutzer mithilfe des obigen Abschnitts konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="4fd30-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

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

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="4fd30-252">Benutzer-und Rollen Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4fd30-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="4fd30-253">Im folgenden Beispiel werden die Navigations Eigenschaften für alle Beziehungen von Benutzer und Rolle mithilfe des obigen Abschnitts konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="4fd30-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

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

<span data-ttu-id="4fd30-254">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="4fd30-254">Notes:</span></span>

* <span data-ttu-id="4fd30-255">Dieses Beispiel enthält auch die `UserRole` Join-Entität, die benötigt wird, um die m:n-Beziehung von Benutzern zu Rollen zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="4fd30-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="4fd30-256">Denken Sie daran, die Typen der Navigations Eigenschaften zu ändern, um widerzuspiegeln, dass `Application{...}` Typen jetzt anstelle von-Typen verwendet werden `Identity{...}` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-256">Remember to change the types of the navigation properties to reflect that `Application{...}` types are now being used instead of `Identity{...}` types.</span></span>
* <span data-ttu-id="4fd30-257">Denken Sie daran, das `Application{...}` in der generischen Definition zu verwenden `ApplicationContext` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-257">Remember to use the `Application{...}` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="4fd30-258">Alle Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="4fd30-258">Add all navigation properties</span></span>

<span data-ttu-id="4fd30-259">Im folgenden Beispiel werden die Navigations Eigenschaften für alle Beziehungen für alle Entitäts Typen mithilfe des obigen Abschnitts konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="4fd30-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

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

### <a name="use-composite-keys"></a><span data-ttu-id="4fd30-260">Verwenden von zusammengesetzten Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="4fd30-260">Use composite keys</span></span>

<span data-ttu-id="4fd30-261">In den vorherigen Abschnitten wurde veranschaulicht, wie Sie den im Modell verwendeten Schlüsseltyp ändern Identity .</span><span class="sxs-lookup"><span data-stu-id="4fd30-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="4fd30-262">Das Ändern des Identity Schlüssel Modells zur Verwendung von zusammengesetzten Schlüsseln wird nicht unterstützt oder empfohlen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="4fd30-263">Die Verwendung eines zusammengesetzten Schlüssels mit Identity umfasst das Ändern der Identity interagiert des Manager-Codes mit dem Modell.</span><span class="sxs-lookup"><span data-stu-id="4fd30-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="4fd30-264">Diese Anpassung geht über den Rahmen dieses Dokuments hinaus.</span><span class="sxs-lookup"><span data-stu-id="4fd30-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="4fd30-265">Ändern von Tabellen-/Spaltennamen und Facetten</span><span class="sxs-lookup"><span data-stu-id="4fd30-265">Change table/column names and facets</span></span>

<span data-ttu-id="4fd30-266">Um die Namen von Tabellen und Spalten zu ändern, wird aufgerufen `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="4fd30-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="4fd30-267">Fügen Sie dann die Konfiguration hinzu, um die Standardwerte zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="4fd30-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="4fd30-268">So ändern Sie z. b. den Namen aller Identity Tabellen:</span><span class="sxs-lookup"><span data-stu-id="4fd30-268">For example, to change the name of all the Identity tables:</span></span>

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

<span data-ttu-id="4fd30-269">In diesen Beispielen werden die Standard Identity Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4fd30-269">These examples use the default Identity types.</span></span> <span data-ttu-id="4fd30-270">Wenn Sie einen app-Typ wie verwenden `ApplicationUser` , konfigurieren Sie diesen Typ anstelle des Standard Typs.</span><span class="sxs-lookup"><span data-stu-id="4fd30-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="4fd30-271">Im folgenden Beispiel werden einige Spaltennamen geändert:</span><span class="sxs-lookup"><span data-stu-id="4fd30-271">The following example changes some column names:</span></span>

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

<span data-ttu-id="4fd30-272">Einige Typen von Daten Bank Spalten können mit bestimmten *Facetten* konfiguriert werden (z. b. die maximal `string` zulässige Länge).</span><span class="sxs-lookup"><span data-stu-id="4fd30-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="4fd30-273">Im folgenden Beispiel werden Spalten maximale Länge für mehrere `string` Eigenschaften im Modell festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4fd30-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

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

### <a name="map-to-a-different-schema"></a><span data-ttu-id="4fd30-274">Einem anderen Schema zuordnen</span><span class="sxs-lookup"><span data-stu-id="4fd30-274">Map to a different schema</span></span>

<span data-ttu-id="4fd30-275">Schemas können sich über Datenbankanbieter hinweg unterschiedlich verhalten.</span><span class="sxs-lookup"><span data-stu-id="4fd30-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="4fd30-276">Für SQL Server werden standardmäßig alle Tabellen im *dbo* -Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="4fd30-277">Die Tabellen können in einem anderen Schema erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="4fd30-278">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4fd30-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="4fd30-279">Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="4fd30-279">Lazy loading</span></span>

<span data-ttu-id="4fd30-280">In diesem Abschnitt wird die Unterstützung für Lazy-Load-Proxys im Identity Modell hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4fd30-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="4fd30-281">Lazy-Load ist nützlich, da Navigations Eigenschaften verwendet werden können, ohne zuvor sicherzustellen, dass Sie geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4fd30-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="4fd30-282">Entitäts Typen können auf verschiedene Weise für Lazy-Load geeignet gemacht werden, wie in der [EF Core-Dokumentation](/ef/core/querying/related-data#lazy-loading)beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4fd30-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="4fd30-283">Verwenden Sie zur Vereinfachung das verzögerte Laden von Proxys, für die Folgendes erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="4fd30-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="4fd30-284">Installation des Pakets " [Microsoft. entityframeworkcore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) ".</span><span class="sxs-lookup"><span data-stu-id="4fd30-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="4fd30-285">Ein Aufrufe von <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> in [adddbcontext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="4fd30-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="4fd30-286">Öffentliche Entitäts Typen mit `public virtual` Navigations Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="4fd30-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="4fd30-287">Im folgenden Beispiel wird das Aufrufen von `UseLazyLoadingProxies` in veranschaulicht `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4fd30-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="4fd30-288">In den vorangehenden Beispielen finden Sie Anleitungen zum Hinzufügen von Navigations Eigenschaften zu den Entitäts Typen.</span><span class="sxs-lookup"><span data-stu-id="4fd30-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4fd30-289">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4fd30-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
