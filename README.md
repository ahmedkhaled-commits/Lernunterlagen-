# Mein Lernpfad-
Lernunterlagen 
# Linux-Grundlagen auf Ubuntu

> Meine persönliche Zusammenfassung aller Basics, die ich mir auf meiner AWS EC2 Ubuntu-Instance draufgeschafft habe.

## Worum geht's hier?

Diese Zusammenfassung deckt alle Linux-Basics ab, die ich täglich brauche, sobald ich auf einem Server arbeite. Von Navigation über Datei-Handling bis hin zu Diensten und dem Filter-Trick mit Pipes.

## 1. Navigation — Wo bin ich, was ist hier?

Das Terminal ist wie ein Datei-Explorer ohne Fenster — ich bin immer in genau einem Ordner. Diese drei Befehle sind das Fundament:

bash pwd
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

## 9. Schnellreferenz — Alle Befehle auf einen Blick

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

---

## Was kommt als Nächstes?

- `chmod` & `chown` — Rechte und Besitzer von Dateien ändern
- `tail` & `less` — Log-Dateien lesen (fürs Debugging)
- Netzwerk-Analyse — `ss`, `curl`, `dig`, `tcpdump`
- Dann: Python lernen und der Report-Generator als Portfolio-Projekt

---

*Ende — Ahmed Mayyas*
