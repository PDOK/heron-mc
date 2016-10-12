#Buildinstructies Heron
Met deze Dockerfile kan snel een build-VM voor Heron worden opgezet. Deze bevat alle benodigde tools (Ant, rsync, zip) en Python-dependencies (Jinja2, Sphinx, etc.) om Heron te bouwen. In de build.xml-bestanden wordt o.a. de PATH-variabele vervangen, waardoor sphinx-build niet meer gevonden kan worden. Hierdoor is een oplossing als Cygwin niet meer geschikt.

Uitgangspunten:

* Windows ontwikkelomgeving;
* Git;
* Docker for Windows (installeert VirtualBox en initialiseert een VM met de naam "default").

## Heron-mc klonen
* Check op je machine Heron uit inclusief alle submodules: `git clone --recursive -j8 https://github.com/PDOK/heron-mc.git`
  * Als je al een kloon van Heron hebt, kun je het volgende commando gebruiken: `git submodule update --init --recursive` (vanuit de heron-mc directory)
  * Merk op dat door het clonen van de submodules de volgende fouten optreden: `fatal: read error: Invalid argument`. Hier is nog geen oplossing voor, maar voor het bouwen lijkt het niet van toepassing.
  * De submodules zijn nodig, omdat er gebruik wordt gemaakt van het bestand license.txt in heron/ux/gxp/git.

## Aanpasingen VirtualBox
* Pas de configuratie aan van de default-VM in VirtualBox:
  * Voeg een nieuwe gedeelde map toe voor je heron-mc directory, of een parent-directory (of de drive). Ik heb mijn D-schijf gedeeld. Pad: "D:\", naam: "D_DRIVE". Vink ook de opties "automatisch" en "permanent" aan.
* Herstart de VM.
* Open de VM om de gedeelde map toe te voegen:
  * Maak eerst een mount point aan, bijv. /mnt/d: `mkdir /mnt/d`.
  * Mount de gedeelde map: `mount -t vboxsf D_DRIVE /mnt/d`.
  * Controleer indien je geen foutmelding hebt gekregen of de mount gelukt is. Met `ls /mnt/d` moet je de inhoud van je D-schijf zien.
  * TODO: automount? Ik heb nog niet gekeken of na een reboot (ook van de ontwikkel PC)de VM weer wordt gestart met gemounte schijf. Eventueel een entry aan /etc/fstab toevoegen.

## Docker-commando's
In de Dockerfile is al een volume toegevoegd me de naam /heron-mc.

### build
```docker build --rm -t heron:0.1  . ```

### run
Heron heb ik zelf uitgecheckt in D:\dev\git\heron-mc. Pas in het run-commando je pad aan. Gebruik de Linux-notatie, uitgaande van het mount point dat je in de VM hebt aangemaakt (/mnt/d).
```docker run -d -ti --name heron -v /mnt/d/dev/git/heron-mc:/heron-mc heron:0.1```

### toegang tot image
```docker attach heron```

### Heron builden, vanuit de image
```
cd /heron-mc/heron/build
make zip
```
In de build-directory is het bestand heron-&lt;versie&gt;.zip toegevoegd
Ga met Ctrl+P, Ctrl+Q weer terug naar je Docker shell, of met `exit` als je de image gelijk wilt stoppen.

### stop
* `docker stop heron`
* `docker rm heron`

of `docker rm -f heron` (verwijderen met geforceerde stop).

