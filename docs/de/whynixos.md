---
title: Warum Nix/NIXOS verwenden?
---

Eine berechtigte Frage! Warum sollte man das alles verwenden? Warum alles, was man über Linux weiß, hinter sich lassen und dieses Nischen-Betriebssystem verwenden? Um das zu verstehen, müssen wir zunächst die Vorteile von Nix betrachten, dem leistungsstarken Paketmanager, auf dem NixOS basiert.

Vorteile von Nix:

- **Der Nix Store**: Nix speichert alle Pakete in einem speziellen Ordner namens Nix Store, der sich im /nix Verzeichnis befindet. Im Nix Store wird ein Paket zusammen mit seine dependencies erstellt und als Hash gespeichert. Der Store ist schreibgeschützt, was bedeutet, dass keine Änderungen daran vorgenommen werden können, ohne spezielle Befehle zu verwenden – _nicht einmal als root_!
- **Atomare Updates**: Wenn von Nix installierte Pakete aktualisiert werden müssen, erlaubt Nix Updates nur dann zu, wenn sie alle erfolgreich sind. Dadurch können fehlerhafte Pakete nicht installiert werden. Wenn ein Paket nicht kompiliert werden kann, warten Sie einfach auf das nächste Update, bei dem das Problem behoben wird! Das dauert in der Regel nur ein paar Tage, während Ihr System vollständig nutzbar und funktionsfähig bleibt.
- **Reproduzierbare Builds**: Wenn Nix ein Paket baut, entsteht jedes Mal genau dasselbe Ergebnis, egal auf welchem System es gebaut wird.
- **Ein Paketmanager für alles**: Müde von NPM, pip, dem AUR und all den anderen Paketmanagern? Wir auch! Deshalb freuen wir uns, Ihnen mitzuteilen, dass Nix über 100.000 Pakete zur Installation bietet und der aktuellste Paketmanager ist, der existiert! Wir betrachten es gerne als die Regel 34 der Packetmanager: Wenn es existiert, ist es in nixpkgs. Falls nicht, wird es noch hinzugefügt.
- **Ideal für die Entwicklung**: Die Entwicklung ist ein Hauptgrund für die Existenz von Nix! Mit Tools wie "direnv" ist das Erstellen von Entwicklungsumgebungen einfach und profitiert von allen Vorteilen, die Nix zu bieten hat.
- **Einfaches Ausprobieren von Paketen ohne Installation mit `nix shell` oder `nix run`**: Wollten Sie schon einmal ein Programm aus der KDE-Suite ausprobieren? Auf anderen Systemen müssen Sie zuerst alle dependencies von KDE installieren. Wenn Ihnen das Programm dann nicht gefällt und Sie es deinstallieren, bleiben diese dependencies dennoch auf Ihrem System. Mit den oben genannten Befehlen können Sie jedes Programm schnell und effektiv ausprobieren. Wenn es Ihnen nicht gefällt, kein Problem! Wenn Sie es entfernen, wird nichts auf Ihrem System verblieben.
- **Verwendet Binär-Caches**: Nix baut Pakete in seinem Repository automatisch remote, sodass eine lokale Kompilierung für alles, was in nixpkgs zu finden ist, unnötig ist. Benötigen Sie einen eigenen Binär-Cache für Ihr Programm? Das lässt sich ebenfalls leicht einrichten!
- **Portabel**: Nix läuft überall: Linux, macOS, WSL und es gibt sogar ein Projekt namens "nix-on-droid", mit dem man es auf Android nutzen kann.
- **Verschiedene Paketversionen ohne Konflikte kombinieren**: Da Pakete unabhängig voneinander gebaut werden, einschließlich aller dependencies, können mehrere Versionen eines Pakets gleichzeitig installiert werden – ohne zusätzliche Konfiguration. Das ist standardmäßig so.

Da Sie jetzt wissen warum Sie Nix verwenden sollten, warum NixOS verwenden?

Vorteile von NixOS:

- **Deklarative Konfiguration**: Das Herzstück von NixOS ist neben dem Paketmanager, das Modulsystem. Module sind im Wesentlichen Optionen, die in Ihrer NixOS-Konfiguration referenziert werden können. Dies bietet zwei Hauptvorteile:
  - **_Einfache Konfiguration_**: Schluss mit der manuellen Einrichtung von Diensten oder der Nutzung von Docker-Containern. In NixOS kann alles, wofür es ein Modul gibt, mit nur wenigen Zeilen Code konfigurieren.
  - **_Leichtere Wartung der Konfiguration_**: Da Ihre Konfiguration aus wenigen Zeilen Code besteht und immer in demselben Verzeichnis liegt, müssen Sie keine Vielzahl von Befehlen ausführen, um Probleme zu lösen. Alles was Sie machen müssen ist legidlich einige Werte in Ihrer NixOS-Konfiguration ändern.
- **Reproduzierbares System**: Eine der wichtigsten Funktionen von NixOS: die Reproduzierbarkeit von Nix auf die Ebene des gesamten Betriebssystems. Sie können Ihre Konfiguration in ein Git-Repository sichern und auf beliebig viele Computer übertragen. Es wird jedes Mal exakt dasselbe Betriebssystem sein – abzüglich von Ihrem /home Verzeichnis. Das alles erleichtert Backups sehr.
- **Integrierte Rollback-Funktion**: Ein weiteres Highlight von NixOS (und anderen unveränderlichen Distributionen). Gibt es ein Problem mit Ihrem aktuellen System? Kein Problem! Rollen Sie einfach auf ein vorheriges System zurück – entweder über das Terminal oder beim Booten.
- **Konfigurationsoptionen für viele Programme und Dienste**: Es stehen tausende von Module und Optionen zur Auswahl in NixOS, alle in einer einzigen Sprache geschrieben.
- **Keine Nebeneffekte – Tatsächliches Entfernen von Paketen und deren dependencies**: Da dependencies mit den Paketen, die sie benötigen, installiert und gespeichert werden, bleiben keine zurück, wenn ein Paket deinstalliert wird. Und überflüssige Verzeichnisse im nix store können mit `nix-collect-garbage` aufgeräumt werden.
- **Einfache Einrichtung von VMs**: Sie können QEMU/KVM ganz einfach mit dem entsprechenden Modul einrichten. Keine Mühen mehr mit systemd oder unübersichtlichen GUIs. Nix kann alles!
- **Andere Konfigurationen testen**: Erinnern Sie sich an `nix shell` und `nix run`? Beide Befehle können auch Konfigurationen aus einer URL bauen! Verweisen Sie Nix einfach auf das Git-Repository, in dem sich die Konfiguration befindet, und es wird den Rest eigenständig erledigen.

Falls einer dieser Punkte für Sie gut klingt, gehen Sie zur nächsten Seite, um zu erfahren wie Sie NixOS auf Ihrem Rechner installieren können.
