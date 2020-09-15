---
title: Aufzählungs Unterstützung-Code First-EF6
description: Aufzählungs Unterstützung-Code First in Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-types/enums
ms.openlocfilehash: e9e1412afb097cc4c944be63130dba0496a80f14
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073976"
---
# <a name="enum-support---code-first"></a>Aufzählungs Unterstützung-Code First
> [!NOTE]
> **Nur EF5** : die Features, APIs usw., die auf dieser Seite erläutert wurden, wurden in Entity Framework 5 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.

In diesem Video und der schrittweisen exemplarischen Vorgehensweise wird die Verwendung von Enumerationstypen mit Entity Framework Code First erläutert. Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.

In dieser exemplarischen Vorgehensweise wird Code First verwendet, um eine neue Datenbank zu erstellen, aber Sie können Code First auch verwenden, um eine Zuordnung [zu einer vorhandenen Datenbank](xref:ef6/modeling/code-first/workflows/existing-database)herzustellen.

Die Aufzählungs Unterstützung wurde in Entity Framework 5 eingeführt. Wenn Sie die neuen Funktionen wie Enumerationstypen, räumliche Datentypen und Tabellenwert Funktionen verwenden möchten, müssen Sie .NET Framework 4,5-Zielversion verwenden. Visual Studio 2012 hat standardmäßig .NET 4,5 als Ziel.

In Entity Framework kann eine Enumeration die folgenden zugrunde liegenden Typen aufweisen: **Byte**, **Int16**, **Int32**, **Int64** oder **SByte**.

## <a name="watch-the-video"></a>Video ansehen
In diesem Video wird gezeigt, wie Enumerationstypen mit Entity Framework Code First verwendet werden. Außerdem wird veranschaulicht, wie Sie-auf--Aufstände in einer LINQ-Abfrage verwenden.

**Präsentiert von**: Julia kornich

**Video**: [WMV](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.wmv)  |  [MP4](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-mp4video-enumwithcodefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/A/5/8/A583DEE8-FD5C-47EE-A4E1-966DDF39D1DA/HDI-ITPro-MSDN-winvideo-enumwithcodefirst.zip)

## <a name="pre-requisites"></a>Voraussetzungen

Sie müssen Visual Studio 2012, Ultimate, Premium, Professional oder Web Express Edition installiert haben, um diese exemplarische Vorgehensweise abzuschließen.

 

## <a name="set-up-the-project"></a>Einrichten des Projekts

1.  Öffnen Sie Visual Studio 2012
2.  Zeigen Sie im Menü **Datei** auf **neu**, und klicken Sie dann auf **Projekt** .
3.  Klicken Sie im linken Bereich auf **Visual C \# **, und wählen Sie dann die **Konsolen** Vorlage aus.
4.  Geben Sie " **enumcodefirst** " als Namen des Projekts ein, und klicken Sie auf **OK** .

## <a name="define-a-new-model-using-code-first"></a>Definieren eines neuen Modells mit Code First

Wenn Sie Code First Entwicklung verwenden, schreiben Sie in der Regel .NET Framework Klassen, die ihr konzeptionelles (Domänen-) Modell definieren. Der folgende Code definiert die Department-Klasse.

Der Code definiert auch die departmentnames-Enumeration. Standardmäßig ist die Enumeration vom Typ **int** . Die Name-Eigenschaft für die Department-Klasse ist vom Typ "departmentnames".

Öffnen Sie die Datei Program.cs, und fügen Sie die folgenden Klassendefinitionen ein.

``` csharp
public enum DepartmentNames
{
    English,
    Math,
    Economics
}     

public partial class Department
{
    public int DepartmentID { get; set; }
    public DepartmentNames Name { get; set; }
    public decimal Budget { get; set; }
}
```
 

## <a name="define-the-dbcontext-derived-type"></a>Definieren des abgeleiteten dbcontext-Typs

Zusätzlich zur Definition von Entitäten müssen Sie eine Klasse definieren, die von DbContext abgeleitet ist und DbSet&lt;TEntity&gt;-Eigenschaften bereitstellt. Die DbSet&lt;TEntity&gt;-Eigenschaften informieren den Kontext darüber, welche Typen Sie in das Modell einbeziehen möchten.

Eine Instanz des von DbContext abgeleiteten Typs verwaltet die Entitätsobjekte während der Laufzeit, was das Auffüllen der Objekte mit Daten aus einer Datenbank, die Änderungsnachverfolgung und das persistente Speichern von Daten in der Datenbank umfasst.

Die Typen "dbcontext" und "dbset" werden in der EntityFramework-Assembly definiert. Wir fügen mit dem nuget-Paket "EntityFramework" einen Verweis auf diese dll hinzu.

1.  Klicken Sie in Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen.
2.  Wählen Sie **nuget-Pakete verwalten... aus.**
3.  Wählen Sie im Dialogfeld nuget-Pakete verwalten die Registerkarte **Online** aus, und wählen Sie das Paket **EntityFramework** aus.
4.  Klicken Sie auf **Install** (Installieren).

Beachten Sie, dass neben der EntityFramework-Assembly auch Verweise auf System. ComponentModel. DataAnnotations und System. Data. Entity-Assemblys hinzugefügt werden.

Fügen Sie am Anfang der Program.cs-Datei die folgende using-Anweisung hinzu:

``` csharp
using System.Data.Entity;
```

Fügen Sie in Program.cs die Kontext Definition hinzu. 

``` csharp
public partial class EnumTestContext : DbContext
{
    public DbSet<Department> Departments { get; set; }
}
```
 

## <a name="persist-and-retrieve-data"></a>Persistenz und Abrufen von Daten

Öffnen Sie die Datei Program.cs, in der die Main-Methode definiert ist. Fügen Sie der Main-Funktion den folgenden Code hinzu. Der Code fügt dem Kontext ein neues Abteilungs Objekt hinzu. Anschließend werden die Daten gespeichert. Der Code führt außerdem eine LINQ-Abfrage aus, die eine Abteilung zurückgibt, in der der Name departmentnames. English lautet.

``` csharp
using (var context = new EnumTestContext())
{
    context.Departments.Add(new Department { Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Kompilieren Sie die Anwendung, und führen Sie sie aus. Das Programm erzeugt die folgende Ausgabe:

``` csharp
DepartmentID: 1 Name: English
```
 

## <a name="view-the-generated-database"></a>Anzeigen der generierten Datenbank

Wenn Sie die Anwendung zum ersten Mal ausführen, erstellt die Entity Framework eine Datenbank für Sie. Da Visual Studio 2012 installiert ist, wird die Datenbank auf der localdb-Instanz erstellt. Standardmäßig benennt der Entity Framework die Datenbank nach dem voll qualifizierten Namen des abgeleiteten Kontexts (in diesem Beispiel **enumcodefirst. enumtestcontext**). Nachfolgend wird die vorhandene Datenbank verwendet.  

Beachten Sie Folgendes: Wenn Sie Änderungen am Modell vornehmen, nachdem die Datenbank erstellt wurde, sollten Sie Code First-Migrationen zum Aktualisieren des Datenbankschemas verwenden. Ein Beispiel für die Verwendung von Migrationen finden Sie [unter Code First einer neuen Datenbank](xref:ef6/modeling/code-first/workflows/new-database) .

Gehen Sie folgendermaßen vor, um die Datenbank und die Daten anzuzeigen:

1.  Wählen Sie im Hauptmenü von Visual Studio 2012 die Option SQL Server-Objekt-Explorer **anzeigen** aus  - &gt; **SQL Server Object Explorer**.
2.  Wenn localdb nicht in der Liste der Server enthalten ist, klicken Sie auf **SQL Server** mit der rechten Maustaste, und wählen Sie **Hinzufügen** aus, um eine Verbindung mit der localdb-Instanz herzustellen SQL Server die Standard **Authentifizierung** zu verwenden.
3.  Erweitern Sie den Knoten localdb.
4.  Erweitern Sie den Ordner **Datenbanken** , um die neue Datenbank anzuzeigen, und navigieren Sie zu der **Abteilungs** Tabelle, Code First keine Tabelle erstellt, die dem Enumerationstyp zugeordnet ist.
5.  Um Daten anzuzeigen, klicken Sie mit der rechten Maustaste auf die Tabelle, und wählen Sie **Daten anzeigen**

## <a name="summary"></a>Zusammenfassung

In dieser exemplarischen Vorgehensweise haben wir uns mit der Verwendung von Enumerationstypen mit Entity Framework Code First beschäftigt. 
