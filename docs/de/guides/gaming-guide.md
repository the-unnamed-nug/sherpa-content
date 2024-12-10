---
title: Gaming auf NixOS
---

Gaming könnte ein wichtiges Anliegen hinsichtlich der Kompatibilität von NixOS sein. Zum Glück kann NixOS, wie jede moderne Linux-Distribution, die meisten Spiele genauso gut wie Windows ausführen.

## Treiber-Einrichtung

Sie sollten sicherstellen, dass Sie die Ünterstützung für OpenGL, Vulkan usw. aktivieren. Das können Sie mit Folgenden tun:

```nix
# configuration.nix
hardware.graphics` = {
    enable = true;
    enable32Bit = true; # Aktiviert auch 32-bit Treiber für Tools wie Wine
};
```

Diese Einstellungen werden in der Regel automatisch gesetzt, wenn Sie etwas wie Steam aktivieren, es wäre jedoch ratsam, sie auch für andere Fälle zu verwenden und explizit zu setzen.

### Hybride Grafik

Für viele von Ihnen, die mit NixOS auf einem Laptop spielen möchten, stellt sich die Frage, wie man das System so konfiguriert, dass entweder die Leistung jederzeit durch die Nutzung beider GPUs maximiert wird oder nur die dedizierte GPU beim Spielen verwendet wird.

#### Nvidia

Nvidia bietet hierfür Unterstützung durch Nvidia Prime. Deren „Sync“-Modus ermöglicht die gleichzeitige Nutzung beider GPUs:

```nix
# configuration.nix
hardware.nvidia.prime = {
    sync.enable = true;
  
    # Interne Grafik von einer CPU
    intelBusId = "PCI:0:2:0";
  
    # Oder, falls Sie eine AMD-CPU haben:
    # amdgpuBusId = "PCI:4:0:0";
  
    # Dedizierte Nvidia-Grafikkarte
    nvidiaBusId = "PCI:1:0:0";
};
```

Alternativ gibt es den „offload“-Modus, der die Akkulaufzeit optimiert, indem die dedizierte GPU nur bei Bedarf genutzt wird:

```nix
# configuration.nix
hardware.nvidia.prime = {
    offload.enable = true;
  
    # Siehe untenstehende Anmerkung
    offload.enableOffloadCmd = true;
  
    # Interne Grafik von einer CPU
    intelBusId = "PCI:0:2:0";
  
    # Oder, falls Sie eine AMD-CPU haben:
    # amdgpuBusId = "PCI:4:0:0";
  
    # Dedizierte Nvidia-Grafikkarte
    nvidiaBusId = "PCI:1:0:0";
};
```

Sie können auch `hardware.nvidia.prime.offload.enableOffloadCmd` aktivieren, um einen `nvidia-offload`-Befehl zu aktivieren, der ein Programm automatisch auf die GPU auslagert. Sie können es verwenden, indem Sie den auszuführenden Befehl mit `nvidia-offload` voranstellen, zum Beispiel mit Steam, indem Sie die Startoptionen so setzen:

```
nvidia-offload %command%
```

Zurück zu den NixOS-Optionen: Um die jeweiligen Bus-IDs zu finden, führen Sie einfach `lspci` im Terminal aus. Links wird eine Bus-ID wie „01:00.0“ angezeigt, und rechts eine Beschreibung der Hardware, der die ID entspricht. Sie sollten in der Lage sein, die Bus-ID sowohl für Ihre CPU als auch für Ihre GPU zu identifizieren.

Im Falle von „01:00.0“ sollte die Bus-ID als `PCI:1:0:0` geschrieben werden. Für „00:02.0“ verwenden Sie `PCI:0:2:0`.

Nvidia-Nutzer sollten auch die spezifische Nvidia-Seite für benutzerdefinierte Treiberkonfigurationen beachten.

#### AMD

Bei AMD sollte der Prozess deutlich einfacher sein. Standardmäßig verwendet AMD die dedizierte GPU nicht synchron mit dem Rechner. Sie können ein ähnliches Verhalten wie bei Nvidia für jedes der oben beschriebenen gewünschten Ergebnisse nachbilden. Für Synchronisation können Sie die Umgebungsvariable `DRI_PRIME=1` wie folgt deklarieren:

```nix
# configuration.nix
environment.sessionVariables = {
    DRI_PRIME = "1";
}
```

Wenn Sie möchten, dass die dedizierte GPU nur in Spielen für die Leistung genutzt wird, können Sie Steam anweisen, den Spielstartbefehl wie folgt zu präfixen:

```
DRI_PRIME=1 %command%
```

## Launcher

Es gibt verschiedene Launcher, die im modernen Gaming verwendet werden. Hier sind einige, die Sie unter Linux wahrscheinlich nützlich finden werden.

### Steam

Steam ist ein wichtiger Distributor von Spielen mit erstklassiger Linux-Unterstützung und ausgezeichneter Unterstützung auf NixOS. Es kann ganz einfach wie folgt installiert werden:

```nix
# configuration.nix
programs.steam = {
    enable = true; # Steam aktivieren
};
```

Einige Optionen, die Sie vielleicht beachten sollten:

| Option                                   | Definition                                                                                                |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `programs.steam.package`                 | Erlaubt es Ihnen, ein bestimmtes Steam-Paket zu definieren, zum Beispiel für die Verwendung mit Overrides |
| `programs.steam.protontricks.enable`     | Aktiviert protontricks (ähnlich wie winetricks für Proton)                                                |
| `programs.steam.remotePlay.openFirewall` | Öffnet die Firewall für Steam Remote Play                                                                 |
| `hardware.steam-hardware.enable`         | Aktiviert bestimmte udev-Regeln für Steam-hardware (Controller, HTC Vive, Index, usw.)                    |

Bitte beachten Sie, dass diese Tabelle nicht vollständig ist und die offizielle NixOS-Optionssuche konsultiert werden sollte.


### Heroic Games Launcher

Heroic Games Launcher ist ein neuerer, quelloffener Launcher mit Unterstützung für Spiele von GOG, Epic und Amazon Prime Games, nativ für Linux.

Es gibt keine umfangreiche Optionsdeklaration, aber es kann problemlos als Paket hinzugefügt werden:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    heroic
];
```

### Lutris

Lutris ist ein weiterer quelloffener Launcher mit Unterstützung für viele verschiedene Spiele sowie benutzerdefinierten Installationsprogrammen, um die Kompatibilität für eine Vielzahl von Spielen zu verbessern, wie z. B. Battle.net, EA Games und sogar eigenständige Spiele wie Osu! oder Genshin Impact.

Es kann ebenfalls als Paket installiert werden:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    lutris
];
```

### Bottles

Schließlich ist Bottles ein relativ neuer, auf GTK4 basierender und quelloffener Launcher, der Kompatibilität mit Windows-Spielen und -Software durch vorkonfigurierte Umgebungen bewirbt. Sie unterstützen viele verschiedene Launcher wie Epic Games, EA Launcher und Battle.net.

Während sie ihr Flatpak-Paket empfehlen, empfehlen wir das in nixpkgs enthaltene Paket aufgrund der besseren Unterstützung mit Nix.

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    bottles
];
```

## Nützliche Tools

Über das Starten eines Spiels hinaus verfügt NixOS über viele zusätzliche Programme und Funktionen, die für das Gaming äußerst nützlich sind.

### GameMode

GameMode führt Optimierungen am Hostsystem und/oder Spielprozess durch, um die Leistung zu verbessern.

Es kann deklarativ wie folgt aktiviert und konfiguriert werden:

```nix
# configuration.nix
programs.gamemode = {
    enable = true;
    settings = {
        # Beispielkonfiguration aus nixpkgs
        general = {
            renice = 10;
        };

        # Warnung: GPU-Optimierung haben das Risiko, die Hardware zu beschädigen
        gpu = {
            apply_gpu_optimisations = "accept-responsibility";
            gpu_device = 0;
            amd_performance_level = "high";
        };

        custom = {
            start = "${pkgs.libnotify}/bin/notify-send 'GameMode gestartet'";
            end = "${pkgs.libnotify}/bin/notify-send 'GameMode beendet'";
        };
    };
};
```

Bitte beachten Sie die man-pages für eine vollständige Liste der Einstellungen.

Ein Spiel kann über Steam mit GameMode gestartet werden, indem `gamemoderun %command%` als Startoption eingestellt wird. Dadurch wird GameMode beim Start des Spiels aktiviert.

### Gamescope

Gamescope ist ein weiteres wichtiges Tool, das von Valve entwickelt wurde. Es fungiert als Mikrokompositor, der die Konsistenz von Spielen unter Linux verbessert.

Es wird ebenfalls hervorragend in NixOS unterstützt und kann deklarativ aktiviert und konfiguriert werden:

```nix
# configuration.nix
programs.gamescope = {
    enable = true;
    args = [ # Definieern Sie Argumente, mit denen Gamescope gestartet werden soll.
        # Beispielkonfiguration aus nixpkgs.
        "--rt"
        "--prefer-vk-device 8086:9bc4"   
    ];
};
```

Sie können auch den Fork von ChimeraOS, Gamescope-Session, verwenden, der eine spezielle Gamescope-Sitzung erstellt, die über den Display Manager gestartet werden kann.

```nix
# configuration.nix
programs.steam.gamescopeSession = {
    enable = true;
    args = []; # Funktioniert genauso wie programs.gamescope.args
    env = {}; # Definieren Sie Umgebungsvariablen für die Gamescope-Sitzung.
};
```

### Protonup

Möglicherweise möchten Sie verschiedene Versionen von Proton installieren, wie z.B. den Fork von Glorious Eggroll. Sie können dies tun, indem Sie die protonup CLI installieren:

```nix
# configuration.nix
environment.systemPackages = with pkgs; [
    protonup
];
```

Und dann führen Sie es aus, um die neuste Proton-GE Version zu installieren:

```
protonup
```

Anschließend müssen Sie Steam vollständig neu starten und die Version in Einstellungen -> Kompatibilität auswählen.

## VR

Dieser Abschnitt ist noch in Arbeit. Wenn Sie dazu beitragen möchten, reichen Sie bitte einen PR im [Repository](https://github.com/the-unnamed-nug/sherpa) ein.

Für kabelgebundenes Spielen siehe [Monado](https://github.com/SimulaVR/monado), eine Open-Source-Implementierung der OpenXR-API.

Für kabellose Geräte siehe [WiVRn](https://github.com/WiVRn/WiVRn), einen auf Monado basierenden VR-Streamer für Geräte wie die Quest.

Derzeit können Sie die folgenden Seiten verweisen: [NixOS Wiki](https://wiki.nixos.org/wiki/VR) und [Linux VR Adventures Wiki](https://wiki.nixos.org/wiki/VR).

## Siehe auch

- [Vimjoyers Video](https://www.youtube.com/watch?v=qlfm3MEbqYA)
- [Arch Wiki](https://wiki.archlinux.org/title/Gaming)
- [Linux VR Adventures Wiki](https://lvra.gitlab.io/)
- [r/linux_gaming FAQ](https://www.reddit.com/r/linux_gaming/wiki/faq/)
