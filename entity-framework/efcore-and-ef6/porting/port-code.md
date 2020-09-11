---
title: 'Portieren von EF6 auf EF Core: Portieren eines codebasierten Modells'
description: Spezifische Informationen zum Portieren einer codebasierten Entity Framework 6-Modellanwendung zu Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: d3920e6132576e3a93dd0ffb9dac1412b6e511aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619622"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>Portieren eines codebasierten EF6-Modells auf EF Core

Wenn Sie alle wichtigen Hinweise gelesen haben und bereit für die Portierung sind, finden Sie hier einige Richtlinien für den Anfang.

## <a name="install-ef-core-nuget-packages"></a>Installieren von EF Core-NuGet-Paketen

Installieren Sie das NuGet-Paket für den gewünschten Datenbankanbieter, um EF Core zu verwenden. Wenn Sie SQL Server nutzen möchten, würden Sie beispielsweise `Microsoft.EntityFrameworkCore.SqlServer` installieren. Weitere Informationen finden Sie unter [Datenbankanbieter](xref:core/providers/index).

Wenn Sie beabsichtigen, Migrationen zu verwenden, sollten Sie auch das `Microsoft.EntityFrameworkCore.Tools`-Paket installieren.

Sie müssen das EF6-NuGet-Paket (EntityFramework) nicht deinstallieren, da EF Core und EF6 parallel in derselben Anwendung verwendet werden können. Wenn Sie jedoch nicht beabsichtigen, EF6 in Ihrer Anwendung zu verwenden, kann das Deinstallieren des Pakets dazu beitragen, dass nur zu den Codebestandteilen Kompilierfehler ausgegeben werden, die wirklich Ihre Aufmerksamkeit erfordern.

## <a name="swap-namespaces"></a>Austauschen von Namespaces

Die meisten APIs, die Sie in EF6 verwenden, befinden sich im `System.Data.Entity`-Namespace (und in den zugehörigen Subnamespaces). Die erste Codeänderung besteht darin, den Namespace durch `Microsoft.EntityFrameworkCore` auszutauschen. In der Regel beginnen Sie mit der Codedatei, die den abgeleiteten Kontext enthält, und arbeiten von dort aus weiter, um Kompilierungsfehler zu beheben, sobald diese auftreten.

## <a name="context-configuration-connection-etc"></a>Kontextkonfiguration (z. B. Verbindungen)

Wie unter [Portieren von EF 6 nach EF Core](xref:efcore-and-ef6/porting/index) beschrieben wird, ist EF Core beim Erkennen der Datenbank, mit der eine Verbindung hergestellt werden soll, weniger eigenständig. Sie müssen die `OnConfiguring`-Methode in Ihrem abgeleiteten Kontext überschreiben und die datenbankanbieterspezifische API verwenden, um die Verbindung mit der Datenbank einzurichten.

In den meisten EF6-Anwendungen wird die Verbindungszeichenfolge in der Anwendungsdatei `App/Web.config` gespeichert. In EF Core wird diese Verbindungszeichenfolge mit der `ConfigurationManager`-API gelesen. Möglicherweise müssen Sie einen Verweis auf die Frameworkassembly `System.Configuration` hinzufügen, damit diese API verwendet werden kann.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a>Aktualisieren Ihres Codes

An dieser Stelle ist es wichtig, Kompilierfehler zu beheben und ein Code Review durchzuführen, um festzustellen, ob die Verhaltensänderungen eine Auswirkung zeigen.

## <a name="existing-migrations"></a>Vorhandene Migrationen

Es gibt derzeit keine umsetzbare Möglichkeit, vorhandene EF6-Migrationen auf EF Core zu portieren.

Sie sollten nach Möglichkeit davon ausgehen, dass alle vorherigen Migrationen von EF6 auf die Datenbank angewendet wurden und dann mit der Migration des Schemas von diesem Punkt aus unter Einsatz von EF Core beginnen. Hierfür können Sie den Befehl `Add-Migration` verwenden, um eine Migration hinzuzufügen, sobald das Modell auf EF Core portiert wurde. Anschließend entfernen Sie den gesamten Code aus den Methoden `Up` und `Down` der Gerüstmigration. Nachfolgende Migrationen werden mit dem Modell verglichen, wenn für die anfängliche Migration ein Gerüst erstellt wurde.

## <a name="test-the-port"></a>Testen des Ports

Nur weil Ihre Anwendung erfolgreich kompiliert wird, bedeutet das nicht, dass sie auch erfolgreich auf EF Core portiert wurde. Sie müssen alle Bereiche Ihrer Anwendung testen, um sicherzustellen, dass keine der Verhaltensänderungen Ihre Anwendung beeinträchtigt hat.
