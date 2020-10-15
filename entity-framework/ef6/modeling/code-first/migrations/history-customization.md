---
title: Anpassen der Migrations Verlaufs Tabelle-EF6
description: Anpassen der Migrations Verlaufs Tabelle in Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: f0f474507659d5fbe43ecf2ed7216fb5d1a86779
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066394"
---
# <a name="customizing-the-migrations-history-table"></a>Anpassen der Migrations Verlaufs Tabelle
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

> [!NOTE]
> In diesem Artikel wird davon ausgegangen, dass Sie wissen, wie Code First-Migrationen in einfachen Szenarien verwendet wird. Andernfalls müssen Sie [Code First-Migrationen](xref:ef6/modeling/code-first/migrations/index) lesen, bevor Sie fortfahren.

## <a name="what-is-migrations-history-table"></a>Was ist eine Migrations Verlaufs Tabelle?

Die Migrations Verlaufs Tabelle ist eine Tabelle, die Code First-Migrationen verwendet, um Details zu den auf die Datenbank angewendeten Migrationen zu speichern Standardmäßig ist der Name der Tabelle in der Datenbank \_ \_ migrationhistory und wird beim Anwenden der ersten Migration auf die Datenbank erstellt. In Entity Framework 5 war diese Tabelle eine Systemtabelle, wenn die Anwendung die Microsoft SQL Server-Datenbank verwendet hat. Dies wurde in Entity Framework 6 jedoch geändert, und die Migrations Verlaufs Tabelle ist nicht mehr als Systemtabelle gekennzeichnet.

## <a name="why-customize-migrations-history-table"></a>Gründe für die Anpassung der Migrations Verlaufs Tabelle

Die Migrations Verlaufs Tabelle sollte ausschließlich von Code First-Migrationen verwendet und manuell geändert werden. Manchmal ist die Standardkonfiguration jedoch nicht geeignet, und die Tabelle muss angepasst werden, zum Beispiel:

-   Sie müssen die Namen und/oder Facetten der Spalten ändern, um einen Drittanbieter-Migrations Anbieter zu aktivieren<sup>.</sup>
-   Sie möchten den Namen der Tabelle ändern.
-   Sie müssen ein nicht standardmäßiges Schema für die \_ \_ migrationhistory-Tabelle verwenden.
-   Sie müssen zusätzliche Daten für eine bestimmte Version des Kontexts speichern. Daher müssen Sie der Tabelle eine weitere Spalte hinzufügen.

## <a name="words-of-precaution"></a>Begriffe der Vorsichtsmaßnahme

Das Ändern der Tabelle "Migrations Verlauf" ist zwar leistungsstark, aber Sie müssen darauf achten, Sie nicht zu überlasten. EF Runtime überprüft derzeit nicht, ob die angepasste Migrations Verlaufs Tabelle mit der Laufzeit kompatibel ist. Wenn dies nicht der Fall ist, kann die Anwendung zur Laufzeit unterbrechen oder sich auf unvorhersehbare Weise Verhalten. Dies ist noch wichtiger, wenn Sie mehrere Kontexte pro Datenbank verwenden. in diesem Fall können mehrere Kontexte dieselbe Migrations Verlaufs Tabelle verwenden, um Informationen über Migrationen zu speichern.

## <a name="how-to-customize-migrations-history-table"></a>Wie wird die Migrations Verlaufs Tabelle angepasst?

Bevor Sie beginnen, müssen Sie wissen, dass Sie die Migrations Verlaufs Tabelle erst anpassen können, bevor Sie die erste Migration anwenden. Nun zum Code.

Zunächst müssen Sie eine Klasse erstellen, die von der System. Data. Entity. Migrationen. History. historycontext-Klasse abgeleitet wird. Die historycontext-Klasse wird von der dbcontext-Klasse abgeleitet, sodass das Konfigurieren der Migrations Verlaufs Tabelle mit der Konfiguration von EF-Modellen mit der fließenden API sehr ähnlich ist. Sie müssen lediglich die onmodelcreating-Methode außer Kraft setzen und die-Tabelle mit der flüssigen API konfigurieren.

>[!NOTE]
> In der Regel müssen Sie beim Konfigurieren von EF-Modellen die Basis nicht aufzurufen. Onmodelcreating () aus der überschriebenen onmodelcreating-Methode, da dbcontext. onmodelcreating () leeren Text enthält. Dies ist nicht der Fall, wenn die Migrations Verlaufs Tabelle konfiguriert wird. In diesem Fall besteht der erste Schritt in der onmodelcreating ()-Überschreibung darin, die Basis zu nennen. Onmodelcreating (). Dadurch wird die Migrations Verlaufs Tabelle auf die standardmäßige Weise konfiguriert, die Sie dann in der über schreibenden Methode optimieren.

Nehmen wir an, Sie möchten die Migrations Verlaufs Tabelle umbenennen und Sie in ein benutzerdefiniertes Schema mit dem Namen "admin" einfügen. Außerdem möchten Sie, dass der DBA die Spalte migrationid in Migration ID umbenennen soll \_ . Dies können Sie erreichen, indem Sie die folgende Klasse erstellen, die von historycontext abgeleitet wurde:

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

Sobald Ihr benutzerdefinierter historycontext bereit ist, müssen Sie EF darauf aufmerksam machen, indem Sie ihn über die [Code basierte Konfiguration](https://msdn.com/data/jj680699)registrieren:

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

Das ist ziemlich alles. Nun können Sie die Paket-Manager-Konsole, Enable-Migrationen, Add-Migration und schließlich Update-Database aufrufen. Dies sollte dazu führen, dass der Datenbank eine Migrations Verlaufs Tabelle entsprechend den Details, die Sie in der abgeleiteten historycontext-Klasse angegeben haben, hinzugefügt wird.

![Migrations Verlaufs Tabelle](~/ef6/media/database.png)
