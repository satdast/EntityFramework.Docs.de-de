---
title: Protokollieren und Abfangen von Daten Bank Vorgängen EF6
description: Protokollieren und Abfangen von Daten Bank Vorgängen in Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b5ee7eb1-88cc-456e-b53c-c67e24c3f8ca
uid: ef6/fundamentals/logging-and-interception
ms.openlocfilehash: bb5c3392b4f2e1f291d7ac373d07724f56d0eb30
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616193"
---
# <a name="logging-and-intercepting-database-operations"></a>Protokollieren und Abfangen von Daten Bank Vorgängen
> [!NOTE]
> **Nur EF6 und höher:** Die Features, APIs usw., die auf dieser Seite erläutert werden, wurden in Entity Framework 6 eingeführt. Wenn Sie eine frühere Version verwenden, gelten manche Informationen nicht.  

Ab Entity Framework 6, wenn Entity Framework einen Befehl an die Datenbank sendet, kann dieser Befehl von Anwendungscode abgefangen werden. Dies wird am häufigsten für die Protokollierung von SQL verwendet, kann aber auch verwendet werden, um den Befehl zu ändern oder abzubrechen.  

EF umfasst insbesondere Folgendes:  

- Eine Log-Eigenschaft für den Kontext, die dem DataContext. Log in ähnelt LINQ to SQL  
- Ein Mechanismus, mit dem der Inhalt und die Formatierung der an das Protokoll gesendeten Ausgabe angepasst werden.  
- Bausteine auf niedriger Ebene für die Abfang Funktion mit größerer Kontrolle und Flexibilität  

## <a name="context-log-property"></a>Kontext Protokoll (Eigenschaft)  

Die dbcontext. Database. Log-Eigenschaft kann auf einen Delegaten für jede Methode festgelegt werden, die eine Zeichenfolge annimmt. In der Regel wird es mit jedem TextWriter verwendet, indem es auf die "Write"-Methode dieses Textwriters festgelegt wird. Alle vom aktuellen Kontext generierten SQL-Informationen werden in diesem Writer protokolliert. Mit dem folgenden Code wird z. b. SQL in der Konsole protokolliert:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    // Your code here...
}
```  

Beachten Sie den Kontext. Database. log ist auf Console. Write festgelegt. Das ist alles, was zum Protokollieren von SQL an der Konsole erforderlich ist.  

Fügen wir nun einen einfachen Abfrage-/Einfüge-/Update-Code hinzu, damit wir einige Ausgaben sehen können:  

``` csharp
using (var context = new BlogContext())
{
    context.Database.Log = Console.Write;

    var blog = context.Blogs.First(b => b.Title == "One Unicorn");

    blog.Posts.First().Title = "Green Eggs and Ham";

    blog.Posts.Add(new Post { Title = "I do not like them!" });

    context.SaveChangesAsync().Wait();
}
```  

Dadurch wird die folgende Ausgabe generiert:  

``` SQL
SELECT TOP (1)
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title]
    FROM [dbo].[Blogs] AS [Extent1]
    WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 4 ms with result: SqlDataReader

SELECT
    [Extent1].[Id] AS [Id],
    [Extent1].[Title] AS [Title],
    [Extent1].[BlogId] AS [BlogId]
    FROM [dbo].[Posts] AS [Extent1]
    WHERE [Extent1].[BlogId] = @EntityKeyValue1
-- EntityKeyValue1: '1' (Type = Int32)
-- Executing at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader

UPDATE [dbo].[Posts]
SET [Title] = @0
WHERE ([Id] = @1)
-- @0: 'Green Eggs and Ham' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 12 ms with result: 1

INSERT [dbo].[Posts]([Title], [BlogId])
VALUES (@0, @1)
SELECT [Id]
FROM [dbo].[Posts]
WHERE @@ROWCOUNT > 0 AND [Id] = scope_identity()
-- @0: 'I do not like them!' (Type = String, Size = -1)
-- @1: '1' (Type = Int32)
-- Executing asynchronously at 10/8/2013 10:55:41 AM -07:00
-- Completed in 2 ms with result: SqlDataReader
```  

(Beachten Sie, dass dies die Ausgabe ist, vorausgesetzt, dass bereits eine Daten Bank Initialisierung erfolgt ist. Wenn die Daten Bank Initialisierung nicht bereits erfolgt ist, gibt es eine Menge mehr Ausgabe, in der alle Arbeits Migrationen unter den Deckeln angezeigt werden, um eine neue Datenbank zu suchen oder eine neue Datenbank zu erstellen.)  

## <a name="what-gets-logged"></a>Was wird protokolliert?  

Wenn die Log-Eigenschaft festgelegt ist, wird Folgendes protokolliert:  

- SQL für alle unterschiedlichen Arten von Befehlen. Beispiel:  
    - Abfragen, einschließlich normaler LINQ-Abfragen, ESQL-Abfragen und unformatierte Abfragen von Methoden wie sqlQuery  
    - Einfügungen, Updates und Löschungen, die im Rahmen von SaveChanges generiert werden  
    - Beziehungen zum Laden von Abfragen, z. b. die von generierten Lazy Loading  
- Parameter  
- Gibt an, ob der Befehl asynchron ausgeführt wird.  
- Ein Zeitstempel, der angibt, wann die Ausführung des Befehls gestartet wurde  
- Gibt an, ob der Befehl erfolgreich abgeschlossen wurde oder ob ein Fehler aufgetreten ist, oder ob für Async abgebrochen wurde.  
- Ein Hinweis auf den Ergebniswert.  
- Der ungefähre Zeitaufwand für die Ausführung des Befehls. Beachten Sie, dass dies der Zeitpunkt ist, an dem der Befehl gesendet wird, um das Ergebnis Objekt zurückzubekommen. Es enthält keine Zeit zum Lesen der Ergebnisse.  

Wenn Sie sich die obige Beispielausgabe ansehen, werden alle vier Befehle protokolliert:  

- Die Abfrage, die sich aus dem Kontext Kontext ergibt. Blogs. First  
    - Beachten Sie, dass die Methode zum Abrufen der SQL-Methode für diese Abfrage nicht funktioniert hat, da "First" keine iquerable-Methode bereitstellt, für die "destring" aufgerufen werden kann.  
- Die Abfrage, die sich aus dem verzögerten Laden des Blogs ergibt. Beiträge  
    - Beachten Sie die Parameter Details für den Schlüsselwert, für den Lazy Loading auftritt.  
    - Nur Eigenschaften des-Parameters, die auf nicht standardmäßige Werte festgelegt sind, werden protokolliert. Die Size-Eigenschaft wird z. b. nur angezeigt, wenn Sie ungleich 0 (null) ist.  
- Zwei von savechangesasync resultierende Befehle eine für das Update zum Ändern eines Beitrags Titels, der andere für eine Einfügung zum Hinzufügen eines neuen Beitrags.  
    - Beachten Sie die Parameter Details der Eigenschaften "FK" und "Title".  
    - Beachten Sie, dass diese Befehle asynchron ausgeführt werden.  

## <a name="logging-to-different-places"></a>Protokollierung an verschiedenen Stellen  

Wie oben gezeigt, ist die Protokollierung auf der Konsole sehr einfach. Es ist auch einfach, sich mit verschiedenen Arten von [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)in Arbeitsspeicher, Datei usw. zu protokollieren.  

Wenn Sie mit LINQ to SQL vertraut sind, werden Sie möglicherweise bemerken, dass in LINQ to SQL die Log-Eigenschaft auf das eigentliche TextWriter-Objekt festgelegt ist (z. b. Console. out), während in EF die Log-Eigenschaft auf eine Methode festgelegt ist, die eine Zeichenfolge akzeptiert (z. b. Console. Write oder Console. out. Write). Der Grund hierfür ist, EF von TextWriter zu entkoppeln, indem ein beliebiger Delegat akzeptiert wird, der als Senke für Zeichen folgen fungieren kann. Stellen Sie sich beispielsweise vor, dass Sie bereits über ein Protokollierungs Framework verfügen und eine Protokollierungs Methode wie folgt definiert:  

``` csharp
public class MyLogger
{
    public void Log(string component, string message)
    {
        Console.WriteLine("Component: {0} Message: {1} ", component, message);
    }
}
```  

Dies könnte wie folgt mit der EF-Protokoll Eigenschaft verknüpft werden:  

``` csharp
var logger = new MyLogger();
context.Database.Log = s => logger.Log("EFApp", s);
```  

## <a name="result-logging"></a>Ergebnis Protokollierung  

Die Standard Protokollierung protokolliert Befehls Text (SQL), Parameter und die Zeile "wird ausgeführt" mit einem Zeitstempel, bevor der Befehl an die Datenbank gesendet wird. Eine "Abgeschlossene" Zeile, die die verstrichene Zeit enthält, wird nach der Ausführung des Befehls protokolliert.  

Beachten Sie, dass bei Async-Befehlen die Zeile "abgeschlossen" nicht protokolliert wird, bis die asynchrone Aufgabe tatsächlich abgeschlossen ist, fehlschlägt oder abgebrochen wird.  

Die Zeile "abgeschlossen" enthält unterschiedliche Informationen, je nach Typ des Befehls und ob die Ausführung erfolgreich war.  

### <a name="successful-execution"></a>Erfolgreiche Ausführung  

Für Befehle, die erfolgreich abgeschlossen werden, lautet die Ausgabe "abgeschlossen in x MS mit Ergebnis:", gefolgt von einem Hinweis auf das Ergebnis. Für Befehle, die einen Daten Reader zurückgeben, ist die Ergebnisanzeige der Typ von [DbDataReader](https://msdn.microsoft.com/library/system.data.common.dbdatareader.aspx) , der zurückgegeben wird. Für Befehle, die einen ganzzahligen Wert zurückgeben, z. b. der oben angezeigte Befehl Update, ist die ganze Zahl.  

### <a name="failed-execution"></a>Fehlgeschlagene Ausführung  

Für Befehle, die fehlschlagen, indem eine Ausnahme ausgelöst wird, enthält die Ausgabe die Meldung aus der Ausnahme. Wenn Sie z. b. sqlQuery zum Abfragen einer Tabelle verwenden, die bereits vorhanden ist, führt dies zu einer Protokoll Ausgabe wie der folgenden:  

``` SQL
SELECT * from ThisTableIsMissing
-- Executing at 5/13/2013 10:19:05 AM
-- Failed in 1 ms with error: Invalid object name 'ThisTableIsMissing'.
```  

### <a name="canceled-execution"></a>Ausführung abgebrochen  

Für asynchrone Befehle, bei denen der Task abgebrochen wird, könnte das Ergebnis ein Fehler mit einer Ausnahme sein, da dies der zugrunde liegende ADO.NET-Anbieter häufig tut, wenn versucht wird, abzubrechen. Wenn dies nicht der Fall ist und die Aufgabe ordnungsgemäß abgebrochen wird, sieht die Ausgabe in etwa wie folgt aus:  

```console
update Blogs set Title = 'No' where Id = -1
-- Executing asynchronously at 5/13/2013 10:21:10 AM
-- Canceled in 1 ms
```  

## <a name="changing-log-content-and-formatting"></a>Ändern von Protokoll Inhalt und Formatierung  

Unter der deckt die Database. Log-Eigenschaft ein databaselogformatter-Objekt. Dieses Objekt bindet eine idbcommandinterceptor-Implementierung (siehe unten) effektiv an einen Delegaten, der Zeichen folgen und einen dbcontext akzeptiert. Dies bedeutet, dass Methoden für databaselogformatter vor und nach der Ausführung von Befehlen von EF aufgerufen werden. Diese databaselogformatter-Methoden erfassen und formatieren die Protokoll Ausgabe und senden Sie an den-Delegaten.  

### <a name="customizing-databaselogformatter"></a>Anpassen von databaselogformatter  

Änderungen, die protokolliert und formatiert werden, können erreicht werden, indem Sie eine neue Klasse erstellen, die von databaselogformatter abgeleitet ist, und die Methoden nach Bedarf überschreibt. Die gängigsten Methoden zum Überschreiben lauten:  

- Logcommand – überschreiben Sie diese Einstellung, um zu ändern, wie Befehle protokolliert werden, bevor Sie ausgeführt werden. Standardmäßig ruft logcommand logparameter für jeden Parameter auf. Sie können in Ihrem Außerkraftsetzungs Vorgang dasselbe tun oder Parameter unterschiedlich behandeln.  
- Logresult – überschreiben Sie diese, um zu ändern, wie das Ergebnis der Ausführung eines Befehls protokolliert wird.  
- Logparameter – überschreiben Sie diese Einstellung, um die Formatierung und den Inhalt der Parameter Protokollierung zu ändern.  

Angenommen, es soll nur eine einzelne Zeile protokolliert werden, bevor jeder Befehl an die Datenbank gesendet wird. Dies kann mit zwei über schreibungen erfolgen:  

- Überschreiben von logcommand zum Formatieren und Schreiben der einzelnen Zeile von SQL  
- Überschreiben Sie logresult, um nichts zu tun.  

Der Code sähe in etwa so aus:

``` csharp
public class OneLineFormatter : DatabaseLogFormatter
{
    public OneLineFormatter(DbContext context, Action<string> writeAction)
        : base(context, writeAction)
    {
    }

    public override void LogCommand<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        Write(string.Format(
            "Context '{0}' is executing command '{1}'{2}",
            Context.GetType().Name,
            command.CommandText.Replace(Environment.NewLine, ""),
            Environment.NewLine));
    }

    public override void LogResult<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
    }
}
```  

Zum Protokollieren der Ausgabe können Sie einfach die Write-Methode abrufen, die die Ausgabe an den konfigurierten Schreib Delegaten sendet  

(Beachten Sie, dass dieser Code eine vereinfachte Entfernung von Zeilenumbrüchen als Beispiel bewirkt. Dies funktioniert wahrscheinlich nicht gut für die Anzeige von komplexem SQL.)  

### <a name="setting-the-databaselogformatter"></a>Festlegen von databaselogformatter  

Nachdem eine neue databaselogformatter-Klasse erstellt wurde, muss Sie bei EF registriert werden. Dies erfolgt mithilfe der Code basierten Konfiguration. Kurz gesagt bedeutet dies, dass Sie eine neue Klasse erstellen, die von dbconfiguration in derselben Assembly wie die dbcontext-Klasse abgeleitet ist, und dann setdatabaselogformatter im Konstruktor dieser neuen Klasse aufrufen. Beispiel:  

``` csharp
public class MyDbConfiguration : DbConfiguration
{
    public MyDbConfiguration()
    {
        SetDatabaseLogFormatter(
            (context, writeAction) => new OneLineFormatter(context, writeAction));
    }
}
```  

### <a name="using-the-new-databaselogformatter"></a>Verwenden des neuen databaselogformatter-Objekts  

Dieser neue databaselogformatter wird jetzt verwendet, wenn "Database. log" festgelegt ist. Wenn Sie den Code aus Teil 1 ausführen, führt dies nun zur folgenden Ausgabe:  

```console
Context 'BlogContext' is executing command 'SELECT TOP (1) [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title]FROM [dbo].[Blogs] AS [Extent1]WHERE (N'One Unicorn' = [Extent1].[Title]) AND ([Extent1].[Title] IS NOT NULL)'
Context 'BlogContext' is executing command 'SELECT [Extent1].[Id] AS [Id], [Extent1].[Title] AS [Title], [Extent1].[BlogId] AS [BlogId]FROM [dbo].[Posts] AS [Extent1]WHERE [Extent1].[BlogId] = @EntityKeyValue1'
Context 'BlogContext' is executing command 'update [dbo].[Posts]set [Title] = @0where ([Id] = @1)'
Context 'BlogContext' is executing command 'insert [dbo].[Posts]([Title], [BlogId])values (@0, @1)select [Id]from [dbo].[Posts]where @@rowcount > 0 and [Id] = scope_identity()'
```  

## <a name="interception-building-blocks"></a>Abfang Bausteine  

Bisher haben wir uns mit der Verwendung von "dbcontext. Database. log" zum Protokollieren des von EF generierten SQL beschäftigt. Dieser Code ist jedoch eine relativ schlanke Fassade über einigen gering stufigen Bausteinen für eine allgemeinere Abfang Funktion.  

### <a name="interception-interfaces"></a>Abfang Schnittstellen  

Der Abfang Code basiert auf dem Konzept der Abfang Schnittstellen. Diese Schnittstellen erben von idbinterceptor und definieren Methoden, die aufgerufen werden, wenn EF einige Aktionen ausführt. Die Absicht besteht darin, dass eine Schnittstelle pro Objekttyp abgefangen wird. Die idbcommandinterceptor-Schnittstelle definiert z. b. Methoden, die aufgerufen werden, bevor EF einen Aufruf an ExecuteNonQuery, ExecuteScalar, ExecuteReader und verwandte Methoden sendet. Ebenso definiert die-Schnittstelle Methoden, die aufgerufen werden, wenn jeder dieser Vorgänge abgeschlossen wird. Die zuvor betrachtete databaselogformatter-Klasse implementiert diese Schnittstelle zum Protokollieren von Befehlen.  

### <a name="the-interception-context"></a>Der Abfang Kontext  

Wenn Sie die Methoden betrachten, die für eine der Interceptor Schnittstellen definiert sind, ist es offensichtlich, dass jedem-Befehl ein Objekt vom Typ "dbinterceptioncontext" oder ein von diesem abgeleiteter Typ, wie z. b. dbcommandinterceptioncontext \<\> Dieses Objekt enthält Kontextinformationen zu der Aktion, die EF durch nimmt. Wenn die Aktion z. b. im Namen eines dbcontext ausgeführt wird, ist dbcontext im dbinterceptioncontext enthalten. Analog dazu wird für Befehle, die asynchron ausgeführt werden, das IsAsync-Flag für dbcommandinterceptioncontext festgelegt.  

### <a name="result-handling"></a>Ergebnisverarbeitung  

Die dbcommandinterceptioncontext \<\> -Klasse enthält die Eigenschaften "result", "originalresult", "Exception" und "originalexception". Diese Eigenschaften werden für Aufrufe der Abfang Methoden, die vor der Ausführung des Vorgangs aufgerufen werden, auf Null/0 (null) festgelegt – d. h. für die... Ausführen von Methoden. Wenn der Vorgang ausgeführt wird und erfolgreich ist, werden result und originalresult auf das Ergebnis des Vorgangs festgelegt. Diese Werte können dann in den Abfang Methoden beobachtet werden, die nach der Ausführung des Vorgangs aufgerufen werden – d. h. auf dem... Ausgeführte Methoden. Ebenso werden, wenn der Vorgang ausgelöst wird, die Eigenschaften Exception und originalexception festgelegt.  

#### <a name="suppressing-execution"></a>Unterdrücken der Ausführung  

Wenn ein Interceptor die Ergebnis Eigenschaft festlegt, bevor der Befehl ausgeführt wurde (in einer der... Ausführen von Methoden) dann versucht EF nicht, den Befehl tatsächlich auszuführen, sondern verwendet stattdessen nur das Resultset. Mit anderen Worten: der Interceptor kann die Ausführung des Befehls unterdrücken, aber EF kann fortgesetzt werden, als ob der Befehl ausgeführt wurde.  

Ein Beispiel dafür, wie dies verwendet werden kann, ist die Batch Verarbeitung des Befehls, die bisher mit einem Wrapping Anbieter durchgeführt wurde. Der-Interceptor speichert den Befehl für die spätere Ausführung als Batch, aber würde den Befehl "vorgeben", um EF darauf zu warten, dass der Befehl wie gewohnt ausgeführt wurde. Beachten Sie, dass es mehr als die Implementierung der Batch Verarbeitung erfordert, aber dies ist ein Beispiel dafür, wie die Änderung des Abfang Ergebnisses verwendet werden kann.  

Die Ausführung kann auch durch Festlegen der Exception-Eigenschaft in einer der... Ausführen von Methoden. Dies bewirkt, dass EF fortgesetzt wird, als ob die Ausführung des Vorgangs fehlgeschlagen ist, indem die angegebene Ausnahme ausgelöst wird. Dies kann natürlich dazu führen, dass die Anwendung abstürzt, aber es kann auch eine vorübergehende Ausnahme oder eine andere Ausnahme sein, die von EF behandelt wird. Dies kann z. b. in Testumgebungen verwendet werden, um das Verhalten einer Anwendung zu testen, wenn die Befehlsausführung fehlschlägt.  

#### <a name="changing-the-result-after-execution"></a>Ändern des Ergebnisses nach der Ausführung  

Wenn ein Interceptor die Ergebnis Eigenschaft festlegt, nachdem der Befehl ausgeführt wurde (in einer der... Ausgeführte Methoden) dann verwendet EF das geänderte Ergebnis anstelle des Ergebnisses, das tatsächlich vom Vorgang zurückgegeben wurde. Wenn ein Interceptor die Exception-Eigenschaft nach dem Ausführen des Befehls festlegt, löst EF die festgelegte Ausnahme aus, als ob der Vorgang die Ausnahme ausgelöst hätte.  

Ein Interceptor kann die Exception-Eigenschaft auch auf NULL festlegen, um anzugeben, dass keine Ausnahme ausgelöst werden soll. Dies kann hilfreich sein, wenn die Ausführung des Vorgangs fehlgeschlagen ist, der Interceptor jedoch wünscht, dass EF fortgesetzt wird, als ob der Vorgang erfolgreich war. Dies umfasst in der Regel auch das Festlegen des Ergebnisses, damit EF mit einem Ergebniswert arbeiten kann, wenn er fortgesetzt wird.  

#### <a name="originalresult-and-originalexception"></a>Originalresult und originalexception  

Nachdem EF einen Vorgang ausgeführt hat, werden die Eigenschaften result und originalresult festgelegt, wenn die Ausführung nicht erfolgreich war, oder die Eigenschaften Exception und originalexception, wenn die Ausführung mit einer Ausnahme fehlgeschlagen ist.  

Die Eigenschaften originalresult und originalexception sind schreibgeschützt und werden nur von EF festgelegt, nachdem ein Vorgang tatsächlich ausgeführt wurde. Diese Eigenschaften können nicht durch Interceptors festgelegt werden. Dies bedeutet, dass jeder Interceptor zwischen einer Ausnahme oder einem Ergebnis unterscheiden kann, die von einem anderen Interceptor festgelegt wurde. Dies gilt nicht für die tatsächliche Ausnahme oder das Ergebnis, das beim Ausführen des Vorgangs aufgetreten ist.  

### <a name="registering-interceptors"></a>Interceptors werden registriert  

Sobald eine Klasse, die eine oder mehrere der Abfang Schnittstellen implementiert, erstellt wurde, kann Sie mithilfe der dbintercep-Klasse bei EF registriert werden. Beispiel:  

``` csharp
DbInterception.Add(new NLogCommandInterceptor());
```  

Interceptors können auch auf der Ebene der APP-Domäne mithilfe des Code basierten Konfigurations Mechanismus von dbconfiguration registriert werden.  

### <a name="example-logging-to-nlog"></a>Beispiel: Protokollierung in nlog  

Fügen wir das alles in ein Beispiel ein, das idbcommandinterceptor und [nlog](https://nlog-project.org/) für Folgendes verwendet:  

- Protokolliert eine Warnung für jeden Befehl, der nicht asynchron ausgeführt wird.  
- Protokolliert einen Fehler für jeden Befehl, der beim Ausführen auslöst.  

Hier ist die Klasse, die die Protokollierung durchführt, die wie oben beschrieben registriert werden sollte:  

``` csharp
public class NLogCommandInterceptor : IDbCommandInterceptor
{
    private static readonly Logger Logger = LogManager.GetCurrentClassLogger();

    public void NonQueryExecuting(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void NonQueryExecuted(
        DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ReaderExecuting(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ReaderExecuted(
        DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    public void ScalarExecuting(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfNonAsync(command, interceptionContext);
    }

    public void ScalarExecuted(
        DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
    {
        LogIfError(command, interceptionContext);
    }

    private void LogIfNonAsync<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (!interceptionContext.IsAsync)
        {
            Logger.Warn("Non-async command used: {0}", command.CommandText);
        }
    }

    private void LogIfError<TResult>(
        DbCommand command, DbCommandInterceptionContext<TResult> interceptionContext)
    {
        if (interceptionContext.Exception != null)
        {
            Logger.Error("Command {0} failed with exception {1}",
                command.CommandText, interceptionContext.Exception);
        }
    }
}
```  

Beachten Sie, dass dieser Code den Abfang Kontext verwendet, um zu ermitteln, wann ein Befehl nicht asynchron ausgeführt wird, und um herauszufinden, wann beim Ausführen eines Befehls ein Fehler aufgetreten ist.  
