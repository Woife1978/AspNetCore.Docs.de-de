<a name="dc"></a>

### <a name="add-a-database-context-class"></a>Hinzufügen einer Datenbankkontext-Klasse

Fügen Sie dem Ordner *Models* die folgende `RazorPagesMovieContext`-Klasse hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie der Datei *appsettings.json* eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Hinzufügen der erforderlichen NuGet-Pakete

Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:

```console
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design

```

Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Erstellen Sie das Projekt zur Fehlerüberprüfung.
