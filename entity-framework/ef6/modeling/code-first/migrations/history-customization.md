---
title: Anpassen der Migrations Verlaufs Tabelle-EF6
description: Anpassen der Migrations Verlaufs Tabelle in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: a6cd27f39c648d35d2e0238a10f8a6b351cc1220
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618073"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="7b591-103">Anpassen der Migrations Verlaufs Tabelle</span><span class="sxs-lookup"><span data-stu-id="7b591-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="7b591-104">**Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="7b591-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7b591-105">Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.</span><span class="sxs-lookup"><span data-stu-id="7b591-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="7b591-106">In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7b591-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="7b591-107">Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="7b591-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="7b591-108">Was ist eine Migrations Verlaufs Tabelle?</span><span class="sxs-lookup"><span data-stu-id="7b591-108">What is Migrations History Table?</span></span>

<span data-ttu-id="7b591-109">Die Migrations Verlaufs Tabelle ist eine Tabelle, die Code First-Migrationen verwendet, um Details zu den auf die Datenbank angewendeten Migrationen zu speichern</span><span class="sxs-lookup"><span data-stu-id="7b591-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="7b591-110">Standardmäßig ist der Name der Tabelle in der Datenbank \_ \_ migrationhistory und wird beim Anwenden der ersten Migration auf die Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="7b591-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="7b591-111">In Entity Framework 5 war diese Tabelle eine Systemtabelle, wenn die Anwendung die Microsoft SQL Server-Datenbank verwendet hat.</span><span class="sxs-lookup"><span data-stu-id="7b591-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="7b591-112">Dies wurde in Entity Framework 6 jedoch geändert, und die Migrations Verlaufs Tabelle ist nicht mehr als Systemtabelle gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="7b591-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="7b591-113">Gründe für die Anpassung der Migrations Verlaufs Tabelle</span><span class="sxs-lookup"><span data-stu-id="7b591-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="7b591-114">Die Migrations Verlaufs Tabelle sollte ausschließlich von Code First-Migrationen verwendet und manuell geändert werden.</span><span class="sxs-lookup"><span data-stu-id="7b591-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="7b591-115">Manchmal ist die Standardkonfiguration jedoch nicht geeignet, und die Tabelle muss angepasst werden, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7b591-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="7b591-116">Sie müssen die Namen und/oder Facetten der Spalten ändern, um einen Drittanbieter-Migrations Anbieter zu aktivieren<sup>.</sup></span><span class="sxs-lookup"><span data-stu-id="7b591-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="7b591-117">Sie möchten den Namen der Tabelle ändern.</span><span class="sxs-lookup"><span data-stu-id="7b591-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="7b591-118">Sie müssen ein nicht standardmäßiges Schema für die \_ \_ migrationhistory-Tabelle verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b591-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="7b591-119">Sie müssen zusätzliche Daten für eine bestimmte Version des Kontexts speichern. Daher müssen Sie der Tabelle eine weitere Spalte hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7b591-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="7b591-120">Begriffe der Vorsichtsmaßnahme</span><span class="sxs-lookup"><span data-stu-id="7b591-120">Words of precaution</span></span>

<span data-ttu-id="7b591-121">Das Ändern der Tabelle "Migrations Verlauf" ist zwar leistungsstark, aber Sie müssen darauf achten, Sie nicht zu überlasten.</span><span class="sxs-lookup"><span data-stu-id="7b591-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="7b591-122">EF Runtime überprüft derzeit nicht, ob die angepasste Migrations Verlaufs Tabelle mit der Laufzeit kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="7b591-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="7b591-123">Wenn dies nicht der Fall ist, kann die Anwendung zur Laufzeit unterbrechen oder sich auf unvorhersehbare Weise Verhalten.</span><span class="sxs-lookup"><span data-stu-id="7b591-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="7b591-124">Dies ist noch wichtiger, wenn Sie mehrere Kontexte pro Datenbank verwenden. in diesem Fall können mehrere Kontexte dieselbe Migrations Verlaufs Tabelle verwenden, um Informationen über Migrationen zu speichern.</span><span class="sxs-lookup"><span data-stu-id="7b591-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="7b591-125">Wie wird die Migrations Verlaufs Tabelle angepasst?</span><span class="sxs-lookup"><span data-stu-id="7b591-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="7b591-126">Bevor Sie beginnen, müssen Sie wissen, dass Sie die Migrations Verlaufs Tabelle erst anpassen können, bevor Sie die erste Migration anwenden.</span><span class="sxs-lookup"><span data-stu-id="7b591-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="7b591-127">Nun zum Code.</span><span class="sxs-lookup"><span data-stu-id="7b591-127">Now, to the code.</span></span>

<span data-ttu-id="7b591-128">Zunächst müssen Sie eine Klasse erstellen, die von der System. Data. Entity. Migrationen. History. historycontext-Klasse abgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="7b591-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="7b591-129">Die historycontext-Klasse wird von der dbcontext-Klasse abgeleitet, sodass das Konfigurieren der Migrations Verlaufs Tabelle mit der Konfiguration von EF-Modellen mit der fließenden API sehr ähnlich ist.</span><span class="sxs-lookup"><span data-stu-id="7b591-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="7b591-130">Sie müssen lediglich die onmodelcreating-Methode außer Kraft setzen und die-Tabelle mit der flüssigen API konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7b591-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="7b591-131">In der Regel müssen Sie beim Konfigurieren von EF-Modellen die Basis nicht aufzurufen. Onmodelcreating () aus der überschriebenen onmodelcreating-Methode, da dbcontext. onmodelcreating () leeren Text enthält.</span><span class="sxs-lookup"><span data-stu-id="7b591-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="7b591-132">Dies ist nicht der Fall, wenn die Migrations Verlaufs Tabelle konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="7b591-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="7b591-133">In diesem Fall besteht der erste Schritt in der onmodelcreating ()-Überschreibung darin, die Basis zu nennen. Onmodelcreating ().</span><span class="sxs-lookup"><span data-stu-id="7b591-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="7b591-134">Dadurch wird die Migrations Verlaufs Tabelle auf die standardmäßige Weise konfiguriert, die Sie dann in der über schreibenden Methode optimieren.</span><span class="sxs-lookup"><span data-stu-id="7b591-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="7b591-135">Nehmen wir an, Sie möchten die Migrations Verlaufs Tabelle umbenennen und Sie in ein benutzerdefiniertes Schema mit dem Namen "admin" einfügen.</span><span class="sxs-lookup"><span data-stu-id="7b591-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="7b591-136">Außerdem möchten Sie, dass der DBA die Spalte migrationid in Migration ID umbenennen soll \_ .</span><span class="sxs-lookup"><span data-stu-id="7b591-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="7b591-137">Dies können Sie erreichen, indem Sie die folgende Klasse erstellen, die von historycontext abgeleitet wurde:</span><span class="sxs-lookup"><span data-stu-id="7b591-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

<span data-ttu-id="7b591-138">Sobald Ihr benutzerdefinierter historycontext bereit ist, müssen Sie EF darauf aufmerksam machen, indem Sie ihn über die [Code basierte Konfiguration](https://msdn.com/data/jj680699)registrieren:</span><span class="sxs-lookup"><span data-stu-id="7b591-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

<span data-ttu-id="7b591-139">Das ist ziemlich alles.</span><span class="sxs-lookup"><span data-stu-id="7b591-139">That’s pretty much it.</span></span> <span data-ttu-id="7b591-140">Nun können Sie zur Paket-Manager-Konsole, Enable-Migrationen, Add-Migration und schließlich Update-Database wechseln.</span><span class="sxs-lookup"><span data-stu-id="7b591-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="7b591-141">Dies sollte dazu führen, dass der Datenbank eine Migrations Verlaufs Tabelle entsprechend den Details, die Sie in der abgeleiteten historycontext-Klasse angegeben haben, hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="7b591-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![Migrations Verlaufs Tabelle](~/ef6/media/database.png)
