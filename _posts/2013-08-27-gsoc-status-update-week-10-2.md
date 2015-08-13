---
layout: post
published: true
title: GSoC Status Update – Week 10
date: 2013-08-27 18:20:00 +0200
categories:
- GSoC
tags:
- KDE
- Amarok
---

{% include gsoc_header.md %}

## Fixing

Last week I fixed Amarok 2.x embedded database importer. There were quite a few
problems with handling an external database process:

* if the `QProcess` object received commands (`start()`, `kill()`,
  `terminate()`) from a different thread than that which has created it, it
  resulted in wrong behavior, often in the form of a crash (can't get much
  wronger than *that*)
* the `QProcess` object does **not** encapsulate the process; it's only an
  interface. When a `QProcess` object was destroyed while the process was still
  running, it issued a warning and killed the process with SIGKILL. Normally
  you'd want to stop an ongoing process with a SIGTERM, so manual lifetime
  management is needed
* if the server has stopped (which it does, after a period of inactivity),
  calling `QSqlDatabase::removeDatabase()` resulted in a SIGPIPE signal from
  inside the MySQL driver
* related to that, an old `QSqlDatabase` connection silently failed to work if
  the server has been restarted. There would be no warnings on
  `QSqlDatabase::open()`.

There was also a question of waiting for mysqld process to be ready to serve.
After some research I decided to adopt an approach that MySQL startup scripts
(including the "official" script, mysql.server) use, which is to wait for the
server's PID file to be modified. Overall, I'm quite satisfied with the results;
it's reliable and fast enough.

## Creating

By the way, there's a new statistics synchronization target: Clementine.

{% include image.html img="assets/2013/08/clementine-importer.png" title="Importers window" caption="I know how much you like pictures" %}

This brings the total number of importers to six, and marks the end of
implementing new importers. From now on, I intend to focus on existing code
including, but not limited to, read-write capabilities and - of course -
testing.

## Simplifying

You may have noticed one additional target on the screenshot above: "Example."
Another thing I focused on this week was code deduplication and simplifying
creation of new importers. To show off, I prepared a basic "Example" importer.
Below is the *full* C++ code. Bear in mind that aside from the code, importers
need a plugin's *.desktop file and a CMakeLists.txt file. Also bear in mind that
with this code the importer is already fully reconfigurable and instances of it
can be created and removed at user's leisure.

```c++
#include "importers/SimpleImporterConfigWidget.h"
#include "importers/SimpleImporterManager.h"
#include "importers/ImporterProvider.h"
#include "statsyncing/SimpleTrack.h"

using namespace StatSyncing;

class ExampleConfigWidget : public SimpleImporterConfigWidget
{
public:
    explicit ExampleConfigWidget( const QVariantMap &config, QWidget *parent = 0, Qt::WindowFlags f = 0 )
        : SimpleImporterConfigWidget( "Example", config, parent, f ) {}
};

class ExampleProvider : public ImporterProvider
{
public:
    ExampleProvider( const QVariantMap &config, ImporterManager *manager )
        : ImporterProvider( config, manager ) {}

    qint64 reliableTrackMetaData() const  { return Meta::valTitle | Meta::valArtist | Meta::valAlbum; }
    qint64 writableTrackStatsData() const { return 0; }
    QSet<QString> artists()               { return QSet<QString>() << "ExampleArtist"; }

    TrackList artistTracks( const QString &artistName )
    {
        Meta::FieldHash metadata;
        metadata.insert( Meta::valArtist, artistName );
        metadata.insert( Meta::valTitle, "ExampleTitle" );
        metadata.insert( Meta::valAlbum, "ExampleAlbum" );
        metadata.insert( Meta::valRating, 10 );

        return TrackList() << TrackPtr( new SimpleTrack( metadata ) );
    }
};

AMAROK_EXPORT_SIMPLE_IMPORTER_PLUGIN( example, "ExampleImporter", i18n( "Example" ),
                                      i18n( "Example Statistics Importer" ), KIcon( "dialog-ok" ),
                                      ExampleConfigWidget, ExampleProvider )
```

{% include image.html img="assets/2013/08/example-importer.png" title="Creted importer" caption="The effect" %}

As always, you can check out my progress on my [public Amarok clone. The branch
is named `gsoc-importers`.

Thanks for reading!
