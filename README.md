# Linux-Grundlagen auf Ubuntu

> Meine persönliche Zusammenfassung aller Basics, die ich mir auf meiner AWS EC2 Ubuntu-Instance draufgeschafft habe.

## Worum geht's hier?

Diese Zusammenfassung deckt alle Linux-Basics ab, die ich täglich brauche, sobald ich auf einem Server arbeite. Von Navigation über Datei-Handling bis hin zu Rechten, Diensten und dem Lesen von Logs.

---

## 1. Navigation — Wo bin ich, was ist hier?

Das Terminal ist wie ein Datei-Explorer ohne Fenster — ich bin immer in genau einem Ordner. Diese drei Befehle sind das Fundament:

```bash
pwd
```
→ **P**rint **W**orking **D**irectory — zeigt, wo ich gerade stehe. Meistens `/home/ubuntu`.

```bash
ls
```
→ **L**i**s**t — zeigt, was in diesem Ordner liegt. Wie der Blick in den Finder.

```bash
ls -la
```
→ Gleiche Sache, aber ausführlich: auch versteckte Dateien (die mit `.` anfangen), plus Rechte, Besitzer, Größe, Datum.

### Sich bewegen

```bash
cd spielwiese
```
→ **C**hange **D**irectory — in den Ordner "spielwiese" rein.

```bash
cd ..
```
→ Eine Ebene zurück. Die zwei Punkte bedeuten "eine Ebene höher".

```bash
cd ~
```
→ Zurück nach Hause (`/home/ubuntu`). Das `~` ist die Abkürzung fürs Home-Verzeichnis.

```bash
cd -
```
→ Zurück zum vorherigen Ordner (wie "Back" im Browser).

---

## 2. Dateien und Ordner — Anlegen, kopieren, löschen

```bash
touch notiz.txt
```
→ Erstellt eine leere Datei namens `notiz.txt`.

```bash
mkdir notizen
```
→ Erstellt einen neuen Ordner namens `notizen`. **M**a**k**e **Dir**ectory.

```bash
cp notiz.txt kopie.txt
```
→ Kopiert `notiz.txt` in eine neue Datei `kopie.txt`. `cp` braucht **IMMER zwei Angaben**: Quelle → Ziel.

```bash
mv kopie.txt alt.txt
```
→ Verschiebt ODER benennt um. In Linux ist das dasselbe. Auch hier: Quelle → Ziel.

```bash
mv datei.txt ~
```
→ Verschiebt `datei.txt` ins Home-Verzeichnis.

```bash
rm alt.txt
```
→ Löscht die Datei. ⚠️ **ACHTUNG: Es gibt keinen Papierkorb! Weg ist weg.**

> **Merkregel:** `cp` und `mv` brauchen **immer zwei Angaben** — von wo, nach wo.

---

## 3. In Dateien schreiben und lesen

```bash
echo "Hallo Ubuntu"
```
→ Gibt Text auf dem Terminal aus.

```bash
echo "Hallo Ubuntu" > test.txt
```
→ Der Pfeil `>` leitet die Ausgabe in eine Datei um. ⚠️ **ACHTUNG: überschreibt den kompletten Inhalt!**

```bash
echo "Zeile 2" >> test.txt
```
→ Doppelter Pfeil `>>` hängt an, statt zu überschreiben. Merke: **ein Pfeil = ersetzen, zwei Pfeile = anhängen**.

```bash
cat test.txt
```
→ Zeigt den kompletten Inhalt einer Datei im Terminal an.

```bash
nano test.txt
```
→ Öffnet die Datei im Text-Editor nano. Speichern mit `Ctrl+O` + Enter, beenden mit `Ctrl+X`. Das `^` in nano bedeutet Ctrl.

---

## 4. sudo — Der Superuser-Modus

Als normaler User (`ubuntu`) darf ich in meinem eigenen Home-Verzeichnis alles, aber am System (z.B. Software installieren) nichts ändern. Dafür brauche ich `sudo` — kurz für "**s**uper**u**ser **do**".

```bash
whoami
```
→ Zeigt mir, wer ich gerade bin. Ergebnis: `ubuntu` (normaler User).

```bash
sudo whoami
```
→ Führt den Befehl als `root` (Administrator) aus. Ergebnis: `root`.

> **Faustregel:** Alles im Home-Ordner (`~`) → kein sudo. Alles am System (`/etc`, `/var`, Software installieren, Dienste starten) → sudo.

---

## 5. apt — Ubuntus App Store

```bash
sudo apt update
```
→ Lädt die **LISTE** aller verfügbaren Programme neu. Installiert noch nichts! Wie im App Store die Übersicht aktualisieren.

```bash
sudo apt install htop -y
```
→ Installiert das Programm `htop`. Das `-y` beantwortet die "Wirklich?"-Rückfrage automatisch mit Ja.

> ⚠️ **Achtung Tastatur:** Der Bindestrich `-y` ist NICHT dasselbe wie die Tilde `~y`. Sieht ähnlich aus, führt zu Fehler.

---

## 6. Prozesse — Was läuft gerade?

Ein Prozess ist einfach ein Programm, das gerade läuft. Jeder Prozess hat eine eindeutige Nummer, die **PID** (Process ID).

```bash
htop
```
→ Interaktive Live-Ansicht: CPU/RAM oben, Prozesse unten. Mit Pfeiltasten scrollen, mit `q` beenden.

```bash
ps aux
```
→ Listet ALLE Prozesse als Text (Schnappschuss, kein Live-Update). Wichtige Spalten: PID, USER, CPU%, MEM%, COMMAND.

---

## 7. Die Pipe `|` — Der wichtigste Trick in Linux

Die Pipe verbindet zwei Programme: Die Ausgabe des linken wird zum Input des rechten. Statt "auf den Bildschirm" geht die Ausgabe direkt zum nächsten Befehl.

> **Analogie:** Wie ein Kaffeefilter — das Rohmaterial läuft durch, unerwünschtes bleibt hängen, nur das Gute kommt in die Tasse.

```bash
ps aux | grep nginx
```
→ `ps aux` listet ALLE Prozesse → Pipe reicht sie an `grep` weiter → `grep` behält nur Zeilen mit "nginx". Ergebnis: nur nginx-Prozesse.

```bash
ls /etc | grep host
```
→ Zeigt aus dem `/etc`-Ordner nur die Dateien, in deren Name "host" vorkommt.

```bash
cat /var/log/nginx/error.log | grep "404"
```
→ Aus einer riesigen Log-Datei nur die 404-Fehler rausziehen. Bei echten Servern unverzichtbar.

> **Tastatur-Tipp:** Die Pipe `|` auf deutschem Mac-Layout: **Option + 7**.

---

## 8. systemctl — Dienste steuern

Ein **Dienst** (Service) läuft dauerhaft im Hintergrund, auch wenn niemand eingeloggt ist. Beispiele: ssh, nginx, docker, cron. `systemctl` ist die Fernbedienung für all diese Dienste.

```bash
sudo systemctl status nginx
```
→ Wie geht's dem Dienst? Läuft er, seit wann, mit welcher PID, letzte Log-Zeilen. Mit `q` raus.

```bash
sudo systemctl start nginx
```
→ Startet den Dienst.

```bash
sudo systemctl stop nginx
```
→ Stoppt den Dienst.

```bash
sudo systemctl restart nginx
```
→ Neustart (stop + start in einem). Nach Config-Änderungen unverzichtbar.

### Wichtiger Unterschied: active vs. enabled

- **active** — läuft JETZT gerade
- **enabled** — startet AUTOMATISCH beim Booten des Servers

Ein Dienst kann alle Kombinationen davon haben. Nach einem `stop` ist er `inactive`, aber bleibt `enabled` — er würde beim nächsten Reboot wieder starten.

---

## 9. Rechte — Wer darf was mit einer Datei machen?

### Warum überhaupt?

Auf einem Server arbeiten oft viele User. Manche Dateien darfst nur du ändern, andere sind für alle lesbar, wieder andere komplett geheim (SSH-Keys!). Dafür braucht Linux eine **Erlaubnis-Liste** an jeder Datei.

### Der Rechte-Code

Wenn du `ls -la` machst, siehst du links sowas wie `-rw-r--r--`. Das entschlüsselt sich so:

```
- rw- r-- r--
│  │   │   │
│  │   │   └── Alle anderen: nur lesen
│  │   └────── Die Gruppe: nur lesen
│  └────────── Der Besitzer: lesen + schreiben
└───────────── Typ: - = Datei, d = Ordner
```

### Die drei Rechte

| Buchstabe | Name | Bei Dateien | Bei Ordnern |
|---|---|---|---|
| **r** | read | Inhalt lesen | Auflisten dürfen (`ls`) |
| **w** | write | Verändern oder löschen | Neue Dateien anlegen |
| **x** | execute | Als Programm ausführen | Hineingehen dürfen (`cd`) |

Ein `-` an einer Stelle heißt: dieses Recht fehlt.

### Die drei Empfänger

1. **u** (user) — der Besitzer
2. **g** (group) — die Gruppe
3. **o** (others) — alle anderen
4. **a** (all) — Kurzform für alle drei

### chmod — Rechte ändern

Formel: `chmod [wer][+ oder -][was] datei`

```bash
chmod +x script.sh
```
→ Fügt `x` für ALLE hinzu (Standard, wenn keine Zielgruppe angegeben).

```bash
chmod u+x script.sh
```
→ Fügt `x` nur beim **user** (Besitzer) hinzu.

```bash
chmod g-w datei.txt
```
→ Nimmt der **group** das Schreibrecht weg.

```bash
chmod o-r geheim.txt
```
→ Nimmt **others** das Leserecht weg.

### Beispiele aus der Praxis

- `-rw-------` → typisch für SSH-Keys. Nur du darfst lesen/schreiben. SSH weigert sich sonst, den Key zu benutzen.
- `-rwxr-xr-x` → typisch für Programme. Alle dürfen lesen und ausführen, nur der Besitzer darf ändern.
- `drwxr-xr-x` → Standard für Ordner. Alle dürfen reingucken, nur der Besitzer verändert.

---

## 10. Logs lesen — Was hat der Server aufgeschrieben?

### Was sind Logs?

Ein **Log** ist ein Tagebuch, das ein Programm über sich selbst schreibt. Jedes Mal wenn was Wichtiges passiert (Erfolg, Fehler, Warnung), landet eine Zeile mit Zeitstempel in einer Datei. Wenn irgendwas nicht funktioniert, sind Logs der erste Anlaufpunkt.

Fast alle Logs liegen unter **`/var/log/`**.

### Die wichtigsten Log-Dateien

- **`syslog`** — allgemeines System-Log, sammelt alles Mögliche
- **`auth.log`** — jede Anmeldung, jeder `sudo`-Befehl (super wichtig für Security)
- **`kern.log`** — Kernel-Meldungen
- **`dpkg.log`** — Paket-Installationen
- **`nginx/access.log`** und **`nginx/error.log`** — Webserver
- **`cloud-init.log`** — AWS-spezifisch, was beim EC2-Start eingerichtet wurde

> **Log-Rotation:** Dateien wie `auth.log.1` oder `.gz` sind archivierte alte Versionen — Linux packt Logs weg, damit sie nicht endlos wachsen.

### tail — die letzten Zeilen anzeigen

Logs sind oft riesig. `tail` zeigt nur die letzten Zeilen — perfekt für "was ist zuletzt passiert?".

```bash
sudo tail /var/log/auth.log
```
→ Die letzten 10 Zeilen.

```bash
sudo tail -n 30 /var/log/auth.log
```
→ Die letzten 30 Zeilen. `-n <zahl>` = number of lines.

```bash
sudo tail -f /var/log/auth.log
```
→ **Live-Ticker!** Das `-f` = follow. Es bleibt offen und zeigt neue Zeilen sofort, wenn sie geschrieben werden. Beenden mit **Ctrl+C**.

**Praxis-Trick:** `tail -f` in einem Fenster laufen lassen, im zweiten Fenster die Aktion auslösen (z.B. neuer SSH-Login) → Live-Zeile taucht sofort auf. So debuggen Admins Server.

### less — durch große Dateien navigieren

Wenn du eine große Log-Datei komplett durchsuchen willst, ist `less` das Tool.

```bash
sudo less /var/log/auth.log
```

Steuerung in `less`:

| Taste | Aktion |
|---|---|
| Pfeile / Space | Seitenweise blättern |
| **g** | Ganz nach oben |
| **Shift + G** | Ganz nach unten |
| **/suchbegriff** | Suchen (wie Ctrl+F) |
| **n** | Nächster Treffer |
| **N** | Vorheriger Treffer |
| **q** | Raus |

```bash
sudo less -i /var/log/auth.log
```
→ Das `-i` = ignore case. Suche ist dann NICHT case-sensitive (`/accepted` findet auch `Accepted`).

### head — das Gegenteil von tail

```bash
head /var/log/auth.log
```
→ Zeigt die **ersten** 10 Zeilen (statt der letzten).

### Beispiel-Log entschlüsseln

Zeile aus dem `auth.log`:

```
2026-07-15T16:50:42  ip-172-31-14-61  sshd-session[29841]: Accepted publickey for ubuntu from 89.244.82.7
```

- `2026-07-15T16:50:42` — Zeitstempel
- `ip-172-31-14-61` — Server-Name
- `sshd-session[29841]` — Programm (SSH), Prozess-ID
- Rest: Anmeldung akzeptiert für User `ubuntu` von IP `89.244.82.7`

---

## 11. Schnellreferenz — Alle Befehle auf einen Blick

| Befehl | Was er macht |
|---|---|
| `pwd` | Wo bin ich? |
| `ls` / `ls -la` | Was liegt hier? (mit versteckten Dateien) |
| `cd <ordner>` | In Ordner wechseln |
| `cd ..` / `cd ~` / `cd -` | Zurück / Home / Vorheriger Ordner |
| `touch <datei>` | Leere Datei erstellen |
| `mkdir <ordner>` | Ordner erstellen |
| `cp <von> <nach>` | Kopieren |
| `mv <von> <nach>` | Verschieben oder umbenennen |
| `rm <datei>` | Löschen (endgültig!) |
| `echo "text" > datei` | Text in Datei schreiben (überschreiben) |
| `echo "text" >> datei` | Text an Datei anhängen |
| `cat <datei>` | Inhalt anzeigen |
| `nano <datei>` | Datei im Editor öffnen |
| `whoami` | Wer bin ich? |
| `sudo <befehl>` | Als Administrator ausführen |
| `sudo apt update` | Paketliste aktualisieren |
| `sudo apt install <paket> -y` | Programm installieren |
| `htop` | Live-Prozessliste (mit `q` raus) |
| `ps aux` | Alle Prozesse als Text |
| `<befehl> \| grep <text>` | Ausgabe nach Text filtern |
| `sudo systemctl status <dienst>` | Status eines Dienstes |
| `sudo systemctl start/stop/restart <dienst>` | Dienst steuern |
| `chmod +x <datei>` | Ausführbar machen |
| `chmod u+x / g-w / o-r <datei>` | Gezielt Rechte geben/nehmen |
| `tail <datei>` | Letzte 10 Zeilen |
| `tail -f <datei>` | Live-Ticker (Ctrl+C zum Beenden) |
| `less <datei>` | Große Datei durchsuchen |
| `head <datei>` | Erste 10 Zeilen |

---

## Was kommt als Nächstes?

- **Python lernen** — Grundlagen für den Report-Generator
- **Netzwerk-Analyse** — `ss`, `curl`, `dig`, `tcpdump`
- **CI/CD-Pipeline** — die `deploy.yml` fürs automatische Deployen
- **Report-Generator bauen** — mein Portfolio-Projekt
- **AWS-Breite** — S3, IAM, VPC, Load Balancer (Richtung SAA-C03)

---

*Stand: Juli 2026 — Ahmed Mayyas*
