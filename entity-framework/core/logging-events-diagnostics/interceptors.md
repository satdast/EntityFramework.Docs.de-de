---
title: Interceptors-EF Core
description: Abfangen von Daten Bank Vorgängen und anderen Ereignissen
author: ajcvickers
ms.date: 10/08/2020
uid: core/logging-events-diagnostics/interceptors
ms.openlocfilehash: e3b2f1a0f1a97d211bcaba0633955a7fe9c0aa91
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128588"
---
# <a name="interceptors"></a>Interceptors

Entity Framework Core (EF Core)-Interceptors ermöglichen das Abfangen, ändern und/oder Unterdrücken von EF Core Vorgängen. Dies umfasst Datenbankvorgänge auf niedriger Ebene, z. B. das Ausführen eines Befehls, sowie Vorgänge höherer Ebene, z. B. Aufrufe von SaveChanges.

Abfangfunktionen unterscheiden sich von Protokollierung und Diagnose insofern, als sie eine Änderung oder Unterdrückung des abzufangenden Vorgangs ermöglichen. [Einfache Protokollierung](xref:core/logging-events-diagnostics/simple-logging) oder [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) sind bessere Optionen für Protokollierung.

Abfangfunktionen werden pro DbContext-Instanz registriert, wenn der Kontext konfiguriert wird. Verwenden Sie einen [Diagnoselistener](xref:core/logging-events-diagnostics/diagnostic-listeners), um dieselben Informationen abzurufen, jedoch für alle DbContext-Instanzen im Prozess.

## <a name="registering-interceptors"></a>Interceptors werden registriert

Interceptors werden <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A> beim [Konfigurieren einer dbcontext-Instanz](xref:core/dbcontext-configuration/index)mithilfe von registriert. Dies erfolgt in der Regel in einer außer Kraft Setzung von <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> . Beispiel:

<!--
public class ExampleContext : BlogsContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(new TaggedQueryCommandInterceptor());
}
-->
[!code-csharp[RegisterInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterInterceptor)]

Alternativ `AddInterceptors` kann auch als Teil von oder aufgerufen werden, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> Wenn eine-Instanz erstellt wird <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , die an den dbcontext-Konstruktor übergeben werden soll.

> [!TIP]
> On-Konfiguration wird immer noch aufgerufen, wenn adddbcontext verwendet wird oder eine dbcontextoptions-Instanz an den dbcontext-Konstruktor übergeben wird. Dies ist der ideale Ort zum Anwenden der Kontext Konfiguration, unabhängig davon, wie der dbcontext erstellt wird.

Interceptors sind häufig zustandslos. Dies bedeutet, dass eine einzelne Interceptor Instanz für alle dbcontext-Instanzen verwendet werden kann. Beispiel:

<!--
public class TaggedQueryCommandInterceptorContext : BlogsContext
{
    private static readonly TaggedQueryCommandInterceptor _interceptor
        = new TaggedQueryCommandInterceptor();

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.AddInterceptors(_interceptor);
}
-->
[!code-csharp[RegisterStatelessInterceptor](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=RegisterStatelessInterceptor)]

Jede Interceptor Instanz muss eine oder mehrere Schnittstellen implementieren, die von abgeleitet werden <xref:Microsoft.EntityFrameworkCore.Diagnostics.IInterceptor> . Jede Instanz sollte nur einmal registriert werden, auch wenn Sie mehrere Abfang Schnittstellen implementiert. EF Core leiten Ereignisse für jede Schnittstelle nach Bedarf weiter.

## <a name="database-interception"></a>Daten Bank Abfang

> [!NOTE]
> Die Daten Bank Abfang Funktion wurde in EF Core 3,0 eingeführt und ist nur für relationale Datenbankanbieter verfügbar.
> Die SAVEPOINT-Unterstützung wurde in EF Core 5,0 eingeführt.

Die Daten Bank Abfang Funktion auf niedriger Ebene wird in die drei in der folgenden Tabelle dargestellten Schnittstellen aufgeteilt.

| Interceptor                                                            | Daten Bank Vorgänge abgefangen
|:-----------------------------------------------------------------------|-------------------------------------------------
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> | Erstellen von Befehlen</br>Ausführen von Befehlen</br>Befehls Fehler</br>Verwerfen des DbDataReader des Befehls
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> | Öffnen und Schließen von Verbindungen</br>Verbindungsfehler
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbTransactionInterceptor> | Erstellen von Transaktionen</br>Verwenden vorhandener Transaktionen</br>Commit für Transaktionen</br>Rollback von Transaktionen</br>Erstellen und Verwenden von Sicherungs Punkten</br>Transaktionsfehler

Die Basisklassen <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbConnectionInterceptor> und <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbTransactionInterceptor> enthalten keine-op-Implementierungen für jede Methode in der entsprechenden-Schnittstelle. Verwenden Sie die Basisklassen, um zu vermeiden, dass nicht verwendete Abhörmethoden implementiert werden müssen.

Die Methoden für die einzelnen Interceptor Typen sind paarweise, wobei der erste aufgerufen wird, bevor der Daten Bank Vorgang gestartet wird, und der zweite, nachdem der Vorgang abgeschlossen wurde. Beispiel: Beispielsweise <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuting%2A?displayProperty=nameWithType> wird aufgerufen, bevor eine Abfrage ausgeführt wird, und <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor.ReaderExecuted%2A?displayProperty=nameWithType> wird aufgerufen, nachdem die Abfrage an die Datenbank gesendet wurde.

Jedes Methoden paar verfügt sowohl über Synchronisierungs-als auch Async-Variationen. Dies ermöglicht es, dass asynchrone e/a-Vorgänge (z. b. das Anfordern eines Zugriffs Tokens) als Teil des Abfangen eines asynchronen Daten Bank Vorgangs durchgeführt werden.

### <a name="example-command-interception-to-add-query-hints"></a>Beispiel: Befehls Abfang Funktion zum Hinzufügen von Abfrage hinweisen

> [!TIP]
> Sie können [das Beispiel für den Befehl Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CommandInterception) von GitHub herunterladen.

Ein <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbCommandInterceptor> kann verwendet werden, um SQL zu ändern, bevor es an die Datenbank gesendet wird. Dieses Beispiel zeigt, wie Sie die SQL-Datei so ändern, dass Sie einen Abfrage Hinweis enthält.

Häufig ist der schwierigste Teil der Abfang Funktion die Bestimmung, wann der Befehl der Abfrage entspricht, die geändert werden muss. Die SQL-Verarbeitung ist eine Option, Sie ist jedoch tendenziell anfällig. Eine andere Möglichkeit ist die Verwendung [EF Core Abfrage Tags](xref:core/querying/tags) , um jede zu ändernde Abfrage zu kennzeichnen. Beispiel:

<!--
            var blogs1 = context.Blogs.TagWith("Use hint: robust plan").ToList();
-->
[!code-csharp[TaggedQuery](../../../samples/core/Miscellaneous/CommandInterception/Program.cs?name=TaggedQuery)]

Dieses Tag kann dann im Interceptor erkannt werden, da es in der ersten Zeile des Befehls Texts immer als Kommentar eingeschlossen wird. Wenn Sie das-Tag erkennen, wird die SQL-Abfrage geändert, um den entsprechenden Hinweis hinzuzufügen:

<!--
public class TaggedQueryCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        ManipulateCommand(command);

        return result;
    }

    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        ManipulateCommand(command);

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }

    private static void ManipulateCommand(DbCommand command)
    {
        if (command.CommandText.StartsWith("-- Use hint: robust plan", StringComparison.Ordinal))
        {
            command.CommandText += " OPTION (ROBUST PLAN)";
        }
    }
}
-->
[!code-csharp[TaggedQueryCommandInterceptor](../../../samples/core/Miscellaneous/CommandInterception/TaggedQueryCommandInterceptor.cs?name=TaggedQueryCommandInterceptor)]

Beachten Sie Folgendes:

* Der Interceptor erbt von <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbCommandInterceptor> , um zu vermeiden, dass jede Methode in der Interceptor Schnittstelle implementiert werden muss.
* Der Interceptor implementiert sowohl Sync-als auch Async-Methoden. Dadurch wird sichergestellt, dass derselbe Abfrage Hinweis auf Synchronisierungs-und asynchrone Abfragen angewendet wird.
* Der Interceptor implementiert die `Executing` Methoden, die von EF Core mit dem generierten SQL aufgerufen werden, _bevor_ es an die Datenbank gesendet wird. Vergleichen Sie dies mit den `Executed` Methoden, die aufgerufen werden, nachdem der Daten Bank Aufruf zurückgegeben wurde.

Wenn Sie den Code in diesem Beispiel ausführen, wird Folgendes generiert, wenn eine Abfrage gekennzeichnet ist:

```sql
-- Use hint: robust plan

SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b] OPTION (ROBUST PLAN)
```

Wenn eine Abfrage nicht markiert ist, wird Sie dagegen unverändert an die Datenbank gesendet:

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
```

### <a name="example-connection-interception-for-sql-azure-authentication-using-add"></a>Beispiel: Verbindungs Abfang für SQL Azure Authentifizierung mit hinzufügen

> [!TIP]
> Sie können [das Beispiel für den Verbindungs Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/ConnectionInterception) von GitHub herunterladen.

Ein <xref:Microsoft.EntityFrameworkCore.Diagnostics.IDbConnectionInterceptor> kann verwendet werden, um das zu bearbeiten, <xref:System.Data.Common.DbConnection> bevor es zum Herstellen einer Verbindung mit der Datenbank verwendet wird. Dies kann verwendet werden, um ein Azure Active Directory (AAD)-Zugriffs Token abzurufen. Beispiel:

<!--
public class AadAuthenticationInterceptor : DbConnectionInterceptor
{
    public override InterceptionResult ConnectionOpening(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result)
        => throw new InvalidOperationException("Open connections asynchronously when using AAD authentication.");

    public override async ValueTask<InterceptionResult> ConnectionOpeningAsync(
        DbConnection connection,
        ConnectionEventData eventData,
        InterceptionResult result,
        CancellationToken cancellationToken = default)
    {
        var sqlConnection = (SqlConnection)connection;

        var provider = new AzureServiceTokenProvider();
        // Note: in some situations the access token may not be cached automatically the Azure Token Provider.
        // Depending on the kind of token requested, you may need to implement your own caching here.
        sqlConnection.AccessToken = await provider.GetAccessTokenAsync("https://database.windows.net/", null, cancellationToken);

        return result;
    }
}
-->
[!code-csharp[AadAuthenticationInterceptor](../../../samples/core/Miscellaneous/ConnectionInterception/AadAuthenticationInterceptor.cs?name=AadAuthenticationInterceptor)]

> [!TIP]
> [Microsoft. Data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) unterstützt jetzt die Aad-Authentifizierung über die Verbindungs Zeichenfolge. Weitere Informationen finden Sie unter <xref:Microsoft.Data.SqlClient.SqlAuthenticationMethod>.

> [!WARNING]
> Beachten Sie, dass der Interceptor auslöst, wenn zum Öffnen der Verbindung ein Synchronisierungs Versuch erfolgt. Dies liegt daran, dass keine nicht Async-Methode zum Abrufen des Zugriffs Tokens vorhanden ist und es [keine universelle und einfache Möglichkeit gibt, eine asynchrone Methode von einem nicht asynchronen Kontext aus aufzurufen, ohne einen Deadlock zu riskieren](https://devblogs.microsoft.com/dotnet/configureawait-faq/).

> [!WARNING]
> in einigen Situationen wird das Zugriffs Token möglicherweise nicht automatisch vom Azure-Tokenanbieter zwischengespeichert. Abhängig von der Art des angeforderten Tokens müssen Sie hier möglicherweise Ihr eigenes Caching implementieren.

### <a name="example-advanced-command-interception-for-caching"></a>Beispiel: Erweiterte Befehls Abfang Funktion für Caching

> [!TIP]
> Sie können [das Beispiel für das erweiterte Befehls Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) von GitHub herunterladen.

EF Core Interceptors können folgende Aktionen ausführen:

* EF Core, die Ausführung des abzufangenden Vorgangs zu unterdrücken
* Ändern Sie das Ergebnis des Vorgangs, der zurückgemeldet wird, in EF Core

Dieses Beispiel zeigt einen Interceptor, der diese Funktionen verwendet, um sich wie ein primitiver Cache auf zweiter Ebene zu Verhalten. Zwischengespeicherte Abfrageergebnisse werden für eine bestimmte Abfrage zurückgegeben, um einen datenbankroundtrip zu vermeiden.

> [!WARNING]
> Achten Sie darauf, dass Sie das EF Core Standardverhalten auf diese Weise ändern. EF Core Verhalten sich möglicherweise auf unerwartete Weise, wenn ein nicht normales Ergebnis angezeigt wird, das nicht ordnungsgemäß verarbeitet werden kann. In diesem Beispiel werden auch Interceptor Konzepte veranschaulicht. Es ist nicht als Vorlage für eine robuste Cache Implementierung auf zweiter Ebene vorgesehen.

In diesem Beispiel führt die Anwendung häufig eine Abfrage aus, um die aktuelle "tägliche Nachricht" zu erhalten:

<!--
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
-->
[!code-csharp[GetDailyMessage](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=GetDailyMessage)]

Diese Abfrage ist so [gekennzeichnet](xref:core/querying/tags) , dass Sie problemlos im Interceptor erkannt werden kann. Die Idee besteht darin, die Datenbank nur einmal täglich auf eine neue Nachricht abzufragen. Zu einem anderen Zeitpunkt wird von der Anwendung ein zwischengespeichertes Ergebnis verwendet. (Im Beispiel wird eine Verzögerung von 10 Sekunden im Beispiel verwendet, um einen neuen Tag zu simulieren.)

#### <a name="interceptor-state"></a>Interceptor Zustand

Dieser Interceptor ist Zustands behaftet: er speichert die ID und den Meldungs Text der aktuellen täglich abgefragten Nachricht sowie die Zeit, zu der die Abfrage ausgeführt wurde. Aufgrund dieses Status ist auch eine [Sperre](/dotnet/csharp/language-reference/keywords/lock-statement) erforderlich, da das Zwischenspeichern erfordert, dass derselbe Interceptor von mehreren Kontext Instanzen verwendet werden muss.

<!--
    private readonly object _lock = new object();
    private int _id;
    private string _message;
    private DateTime _queriedAt;
-->
[!code-csharp[InterceptorState](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=InterceptorState)]

#### <a name="before-execution"></a>Vor der Ausführung

In der- `Executing` Methode (d. h. vor dem Erstellen eines Daten Bank Aufrufes) erkennt der Interceptor die markierte Abfrage und überprüft dann, ob ein zwischengespeichertes Ergebnis vorliegt. Wenn ein solches Ergebnis gefunden wird, wird die Abfrage unterdrückt, und stattdessen werden zwischengespeicherte Ergebnisse verwendet.

<!--
    public override ValueTask<InterceptionResult<DbDataReader>> ReaderExecutingAsync(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal))
        {
            lock (_lock)
            {
                if (_message != null
                    && DateTime.UtcNow < _queriedAt + new TimeSpan(0, 0, 10))
                {
                    command.CommandText = "-- Get_Daily_Message: Skipping DB call; using cache.";
                    result = InterceptionResult<DbDataReader>.SuppressWithResult(new CachedDailyMessageDataReader(_id, _message));
                }
            }
        }

        return new ValueTask<InterceptionResult<DbDataReader>>(result);
    }
-->
[!code-csharp[ReaderExecutingAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutingAsync)]

Beachten Sie, wie der Code aufruft <xref:Microsoft.EntityFrameworkCore.Diagnostics.InterceptionResult%601.SuppressWithResult%2A?displayProperty=nameWithType> und einen Austausch übergibt, <xref:System.Data.Common.DbDataReader> der die zwischengespeicherten Daten enthält. Dieses interceptionresult wird dann zurückgegeben, wodurch die Abfrage Ausführung unterdrückt wird. Der Ersetzungs Reader wird stattdessen von EF Core als Ergebnisse der Abfrage verwendet.

Dieser Interceptor bearbeitet auch den Befehls Text. Diese Bearbeitung ist nicht erforderlich, verbessert jedoch die Übersichtlichkeit in Protokollmeldungen. Der Befehls Text muss keine gültige SQL-Anweisung sein, da die Abfrage jetzt nicht mehr ausgeführt wird.

#### <a name="after-execution"></a>Nach der Ausführung

Wenn keine zwischengespeicherte Meldung verfügbar ist oder Sie abgelaufen ist, unterdrückt der obige Code das Ergebnis nicht. Die Abfrage wird von EF Core daher wie gewohnt ausgeführt. Nach der Ausführung wird die Methode des Interceptors zurückgegeben `Executed` . Wenn das Ergebnis nicht bereits ein zwischen gespeicherter Reader ist, dann wird die neue Nachrichten-ID und die Zeichenfolge aus dem echten Reader entfernt und für die nächste Verwendung dieser Abfrage bereit zwischengespeichert.

<!--
    public override async ValueTask<DbDataReader> ReaderExecutedAsync(
        DbCommand command,
        CommandExecutedEventData eventData,
        DbDataReader result,
        CancellationToken cancellationToken = default)
    {
        if (command.CommandText.StartsWith("-- Get_Daily_Message", StringComparison.Ordinal)
            && !(result is CachedDailyMessageDataReader))
        {
            try
            {
                await result.ReadAsync(cancellationToken);

                lock (_lock)
                {
                    _id = result.GetInt32(0);
                    _message = result.GetString(1);
                    _queriedAt = DateTime.UtcNow;
                    return new CachedDailyMessageDataReader(_id, message);
                }
            }
            finally
            {
                await result.DisposeAsync();
            }
        }

        return result;
    }
-->
[!code-csharp[ReaderExecutedAsync](../../../samples/core/Miscellaneous/CachingInterception/CachingCommandInterceptor.cs?name=ReaderExecutedAsync)]

#### <a name="demonstration"></a>Demo

Das [Beispiel Caching Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/CachingInterception) enthält eine einfache Konsolenanwendung, die tägliche Nachrichten abfragt, um die Zwischenspeicherung zu testen:

<!--
        // 1. Initialize the database with some daily messages.
        using (var context = new DailyMessageContext())
        {
            await context.Database.EnsureDeletedAsync();
            await context.Database.EnsureCreatedAsync();

            context.AddRange(
                new DailyMessage { Message = "Remember: All builds are GA; no builds are RTM." },
                new DailyMessage { Message = "Keep calm and drink tea" });

            await context.SaveChangesAsync();
        }

        // 2. Query for the most recent daily message. It will be cached for 10 seconds.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 3. Insert a new daily message.
        using (var context = new DailyMessageContext())
        {
            context.Add(new DailyMessage { Message = "Free beer for unicorns" });

            await context.SaveChangesAsync();
        }

        // 4. Cached message is used until cache expires.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        // 5. Pretend it's the next day.
        Thread.Sleep(10000);

        // 6. Cache is expired, so the last message will noe be queried again.
        using (var context = new DailyMessageContext())
        {
            Console.WriteLine(await GetDailyMessage(context));
        }

        #region GetDailyMessage
        async Task<string> GetDailyMessage(DailyMessageContext context)
            => (await context.DailyMessages.TagWith("Get_Daily_Message").OrderBy(e => e.Id).LastAsync()).Message;
        #endregion
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/CachingInterception/Program.cs?name=Main)]

Dadurch wird die folgende Ausgabe zurückgegeben:

```output
info: 10/15/2020 12:32:11.801 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Keep calm and drink tea

info: 10/15/2020 12:32:11.821 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[@p0='Free beer for unicorns' (Size = 22)], CommandType='Text', CommandTimeout='30']
      INSERT INTO "DailyMessages" ("Message")
      VALUES (@p0);
      SELECT "Id"
      FROM "DailyMessages"
      WHERE changes() = 1 AND "rowid" = last_insert_rowid();

info: 10/15/2020 12:32:11.826 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message: Skipping DB call; using cache.

Keep calm and drink tea

info: 10/15/2020 12:32:21.833 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      -- Get_Daily_Message

      SELECT "d"."Id", "d"."Message"
      FROM "DailyMessages" AS "d"
      ORDER BY "d"."Id" DESC
      LIMIT 1

Free beer for unicorns
```

Beachten Sie aus der Protokoll Ausgabe, dass die Anwendung die zwischengespeicherte Nachricht weiterhin verwendet, bis das Timeout abläuft. an diesem Punkt wird die Datenbank für jede neue Nachricht erneut abgefragt.

## <a name="savechanges-interception"></a>Abfangen von SaveChanges

> [!NOTE]
> Das Abfangen von SaveChanges wurde in EF Core 5,0 eingeführt.

> [!TIP]
> Sie können [das Beispiel für das SaveChanges-Interceptor](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) von GitHub herunterladen.

<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> und <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> Abfang Punkte werden von der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor> Schnittstelle definiert. Wie bei anderen Interceptors wird die <xref:Microsoft.EntityFrameworkCore.Diagnostics.SaveChangesInterceptor> Basisklasse ohne op-Methoden als praktische Bereitstellung bereitgestellt.

> [!TIP]
> Interceptors sind leistungsstark. In vielen Fällen kann es jedoch einfacher sein, die SaveChanges-Methode außer Kraft zu setzen oder die [.net-Ereignisse für SaveChanges](xref:core/logging-events-diagnostics/events) zu verwenden, die in dbcontext verfügbar gemacht werden.

### <a name="example-savechanges-interception-for-auditing"></a>Beispiel: SaveChanges-Abfang Vorgänge für die Überwachung

SaveChanges kann abgefangen werden, um einen unabhängigen Überwachungsdaten Satz der vorgenommenen Änderungen zu erstellen.

> [!NOTE]
> Dabei handelt es sich nicht um eine robuste Überwachungslösung. Vielmehr handelt es sich um ein vereinfachtes Beispiel zur Veranschaulichung der Funktionen der Abfang Funktion.

#### <a name="the-application-context"></a>Der Anwendungskontext

Das [Beispiel für](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) die Überwachung verwendet einen einfachen dbcontext mit Blogs und Beiträgen.

<!--
public class BlogsContext : DbContext
{
    private readonly AuditingInterceptor _auditingInterceptor = new AuditingInterceptor("DataSource=audit.db");

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .AddInterceptors(_auditingInterceptor)
            .UseSqlite("DataSource=blogs.db");

    public DbSet<Blog> Blogs { get; set; }
}

public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }

    public Blog Blog { get; set; }
}
-->
[!code-csharp[BlogsContext](../../../samples/core/Miscellaneous/SaveChangesInterception/BlogsContext.cs?name=BlogsContext)]

Beachten Sie, dass für jede dbcontext-Instanz eine neue Instanz des Interceptors registriert wird. Dies liegt daran, dass der Überwachungs Interceptor den mit der aktuellen Kontext Instanz verknüpften Zustand enthält.

#### <a name="the-audit-context"></a>Der Überwachungs Kontext

Das Beispiel enthält auch ein zweites dbcontext-und-Modell, das für die Überwachungs Datenbank verwendet wird.

<!--
public class AuditContext : DbContext
{
    private readonly string _connectionString;

    public AuditContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.UseSqlite(_connectionString);

    public DbSet<SaveChangesAudit> SaveChangesAudits { get; set; }
}

public class SaveChangesAudit
{
    public int Id { get; set; }
    public Guid AuditId { get; set; }
    public DateTime StartTime { get; set; }
    public DateTime EndTime { get; set; }
    public bool Succeeded { get; set; }
    public string ErrorMessage { get; set; }

    public ICollection<EntityAudit> Entities { get; } = new List<EntityAudit>();
}

public class EntityAudit
{
    public int Id { get; set; }
    public EntityState State { get; set; }
    public string AuditMessage { get; set; }

    public SaveChangesAudit SaveChangesAudit { get; set; }
}
-->
[!code-csharp[AuditContext](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditContext.cs?name=AuditContext)]

#### <a name="the-interceptor"></a>Der Interceptor

Die allgemeine Idee für die Überwachung mit dem Interceptor lautet wie folgt:

* Eine Überwachungs Meldung wird zu Beginn von SaveChanges erstellt und in die Überwachungs Datenbank geschrieben.
* "SaveChanges" kann fortgesetzt werden.
* Wenn SaveChanges erfolgreich ist, wird die Überwachungs Meldung aktualisiert, um die erfolgreiche Ausführung anzuzeigen.
* Wenn SaveChanges fehlschlägt, wird die Überwachungs Meldung aktualisiert, um den Fehler anzuzeigen.

Die erste Phase wird behandelt, bevor Änderungen mithilfe von außer Kraft setzungen von und an die Datenbank gesendet werden <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChanges%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavingChangesAsync%2A?displayProperty=nameWithType> .

<!--
    public async ValueTask<InterceptionResult<int>> SavingChangesAsync(
        DbContextEventData eventData,
        InterceptionResult<int> result,
        CancellationToken cancellationToken = default)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            await auditContext.SaveChangesAsync();
        }

        return result;
    }

    public InterceptionResult<int> SavingChanges(
        DbContextEventData eventData,
        InterceptionResult<int> result)
    {
        _audit = CreateAudit(eventData.Context);

        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Add(_audit);
            auditContext.SaveChanges();
        }

        return result;
    }
-->
[!code-csharp[SavingChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavingChanges)]

Durch Überschreiben von Synchronisierungs-und asynchronen Methoden wird sichergestellt, dass die Überwachung unabhängig davon erfolgt, ob `SaveChanges` oder `SaveChangesAsync` aufgerufen werden Beachten Sie auch, dass die asynchrone Überladung selbst eine nicht blockierende asynchrone e/a für die Überwachungs Datenbank ausführen kann. Möglicherweise möchten Sie die Synchronisierungsmethode auslösen, `SavingChanges` um sicherzustellen, dass alle Datenbank-e/a Async ist. Dies erfordert, dass die Anwendung immer `SaveChangesAsync` und nie aufruft `SaveChanges` .

#### <a name="the-audit-message"></a>Die Überwachungs Meldung

Jede Interceptor Methode verfügt über einen- `eventData` Parameter, der Kontextinformationen über das abzufangene Ereignis bereitstellt. In diesem Fall ist der aktuelle Anwendungs-dbcontext in den Ereignisdaten enthalten, die dann zum Erstellen einer Überwachungs Nachricht verwendet werden.

<!--
    private static SaveChangesAudit CreateAudit(DbContext context)
    {
        context.ChangeTracker.DetectChanges();

        var audit = new SaveChangesAudit
        {
            AuditId = Guid.NewGuid(),
            StartTime = DateTime.UtcNow
        };

        foreach (var entry in context.ChangeTracker.Entries())
        {
            var auditMessage = entry.State switch
            {
                EntityState.Deleted => CreateDeletedMessage(entry),
                EntityState.Modified => CreateModifiedMessage(entry),
                EntityState.Added => CreateAddedMessage(entry),
                _ => null
            };

            if (auditMessage != null)
            {
                audit.Entities.Add(new EntityAudit { State = entry.State, AuditMessage = auditMessage });
            }
        }

        return audit;

        string CreateAddedMessage(EntityEntry entry)
            => entry.Properties.Aggregate(
                $"Inserting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateModifiedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.IsModified || property.Metadata.IsPrimaryKey()).Aggregate(
                $"Updating {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");

        string CreateDeletedMessage(EntityEntry entry)
            => entry.Properties.Where(property => property.Metadata.IsPrimaryKey()).Aggregate(
                $"Deleting {entry.Metadata.DisplayName()} with ",
                (auditString, property) => auditString + $"{property.Metadata.Name}: '{property.CurrentValue}' ");
    }
-->
[!code-csharp[CreateAudit](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=CreateAudit)]

Das Ergebnis ist eine `SaveChangesAudit` Entität mit einer Auflistung von `EntityAudit` Entitäten, eine für jede INSERT-, Update-oder DELETE-Auflistung. Der Interceptor fügt diese Entitäten dann in die Überwachungs Datenbank ein.

> [!TIP]
> ToString wird in jeder EF Core Ereignisdaten Klasse überschrieben, um die entsprechende Protokollmeldung für das Ereignis zu generieren. Durch Aufrufen von wird z. b. `ContextInitializedEventData.ToString` "Entity Framework Core 5.0.0" blogscontext "mithilfe des Anbieters" Microsoft. entityframeworkcore. sqlite "mit den Optionen" None "initialisiert.

#### <a name="detecting-success"></a>Erkennen von Erfolg

Die Audit-Entität wird auf dem Interceptor gespeichert, sodass wieder auf Sie zugegriffen werden kann, sobald SaveChanges entweder erfolgreich ist oder fehlschlägt. Für Erfolg <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChanges%2A?displayProperty=nameWithType> wird oder <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SavedChangesAsync%2A?displayProperty=nameWithType> aufgerufen.

<!--
    public int SavedChanges(SaveChangesCompletedEventData eventData, int result)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            auditContext.SaveChanges();
        }

        return result;
    }

    public async ValueTask<int> SavedChangesAsync(
        SaveChangesCompletedEventData eventData,
        int result,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = true;
            _audit.EndTime = DateTime.UtcNow;

            await auditContext.SaveChangesAsync(cancellationToken);
        }

        return result;
    }
-->
[!code-csharp[SavedChanges](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SavedChanges)]

Die Audit-Entität wird an den Überwachungs Kontext angefügt, da Sie bereits in der Datenbank vorhanden ist und aktualisiert werden muss. Anschließend legen wir `Succeeded` und fest `EndTime` , wodurch diese Eigenschaften als geändert markiert werden, sodass SaveChanges ein Update an die Überwachungs Datenbank sendet.

#### <a name="detecting-failure"></a>Erkennen von Fehlern

Fehler werden auf die gleiche Weise behandelt wie Erfolg, aber in der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailed%2A?displayProperty=nameWithType> Methode oder der- <xref:Microsoft.EntityFrameworkCore.Diagnostics.ISaveChangesInterceptor.SaveChangesFailedAsync%2A?displayProperty=nameWithType> Methode. Die Ereignisdaten enthalten die ausgelöste Ausnahme.

<!--
    public void SaveChangesFailed(DbContextErrorEventData eventData)
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.Message;

            auditContext.SaveChanges();
        }
    }

    public async Task SaveChangesFailedAsync(
        DbContextErrorEventData eventData,
        CancellationToken cancellationToken = new CancellationToken())
    {
        using (var auditContext = new AuditContext(_connectionString))
        {
            auditContext.Attach(_audit);
            _audit.Succeeded = false;
            _audit.EndTime = DateTime.UtcNow;
            _audit.ErrorMessage = eventData.Exception.InnerException?.Message;

            await auditContext.SaveChangesAsync(cancellationToken);
        }
    }
-->
[!code-csharp[SaveChangesFailed](../../../samples/core/Miscellaneous/SaveChangesInterception/AuditingInterceptor.cs?name=SaveChangesFailed)]

#### <a name="demonstration"></a>Demo

Das Überwachungs [Beispiel](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/SaveChangesInterception) enthält eine einfache Konsolenanwendung, die Änderungen an der Blog Datenbank vornimmt und dann die erstellte Überwachung anzeigt.

<!--
        // Insert, update, and delete some entities

        using (var context = new BlogsContext())
        {
            context.Add(
                new Blog
                {
                    Name = "EF Blog",
                    Posts =
                    {
                        new Post { Title = "EF Core 3.1!" },
                        new Post { Title = "EF Core 5.0!" }
                    }
                });

            await context.SaveChangesAsync();
        }

        using (var context = new BlogsContext())
        {
            var blog = context.Blogs.Include(e => e.Posts).Single();

            blog.Name = "EF Core Blog";
            context.Remove(blog.Posts.First());
            blog.Posts.Add(new Post { Title = "EF Core 6.0!" });

            context.SaveChanges();
        }

        // Do an insert that will fail

        using (var context = new BlogsContext())
        {
            try
            {
                context.Add(new Post { Id = 3, Title = "EF Core 3.1!" });

                await context.SaveChangesAsync();
            }
            catch (DbUpdateException)
            {
            }
        }

        // Look at the audit trail

        using (var context = new AuditContext("DataSource=audit.db"))
        {
            foreach (var audit in context.SaveChangesAudits.Include(e => e.Entities).ToList())
            {
                Console.WriteLine(
                    $"Audit {audit.AuditId} from {audit.StartTime} to {audit.EndTime} was{(audit.Succeeded ? "" : " not")} successful.");

                foreach (var entity in audit.Entities)
                {
                    Console.WriteLine($"  {entity.AuditMessage}");
                }

                if (!audit.Succeeded)
                {
                    Console.WriteLine($"  Error: {audit.ErrorMessage}");
                }
            }
        }
-->
[!code-csharp[Program](../../../samples/core/Miscellaneous/SaveChangesInterception/Program.cs?name=Program)]

Das Ergebnis zeigt den Inhalt der Überwachungs Datenbank:

```output
Audit 52e94327-1767-4046-a3ca-4c6b1eecbca6 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Blog with Id: '-2147482647' Name: 'EF Blog'
  Inserting Post with Id: '-2147482647' BlogId: '-2147482647' Title: 'EF Core 3.1!'
  Inserting Post with Id: '-2147482646' BlogId: '-2147482647' Title: 'EF Core 5.0!'
Audit 8450f57a-5030-4211-a534-eb66b8da7040 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was successful.
  Inserting Post with Id: '-2147482645' BlogId: '1' Title: 'EF Core 6.0!'
  Updating Blog with Id: '1' Name: 'EF Core Blog'
  Deleting Post with Id: '1'
Audit 201fef4d-66a7-43ad-b9b6-b57e9d3f37b3 from 10/14/2020 9:10:17 PM to 10/14/2020 9:10:17 PM was not successful.
  Inserting Post with Id: '3' BlogId: '' Title: 'EF Core 3.1!'
  Error: SQLite Error 19: 'UNIQUE constraint failed: Post.Id'.
```
