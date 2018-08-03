# Wine Appunti

## Introduzione

Wine è un software che fornisce un layer di compatibilità per i 
programmi Windows per diversi sistemi *NIX, questo ci permette di 
avere prestazioni analoghe a quelle di un sistema Windows. 
Questo software ci da la possibilità di creare infiniti ambienti Windows, 
ognuno con la propria configurazione, ogni ambiente windows è chiamato nel 
gergo wine “wine prefix” o “wine bottle”, nella prossima sezione 
vedremo come creare quindi un ambiente Windows e come 
configurarlo, una volta fatto, ci basterà solo installare 
l'applicazione desiderata, starà a noi poi decidere se 
condividere la “wine bottle” tra più programmi oppure se usare 
una wine bottle per ogni applicazione installata. 
Una wine bottle occupa approssimativamente 40MB.


## Creare un ambiente per un programma Windows

Per creare un ambiente per un programma Windows dobbiamo creare 
un “wine prefix” detto anche "wine bottle", per farlo eseguiamo i 
seguenti comandi da terminale:

```sh
 export WINEPREFIX=~/.wine-mioProgramma 
 # in questo comando definiamo la variabile d'ambiente WINEPREFIX 
 # (che è una delle # variabili d'ambiente che usa wine) che imposterà 
 # il percorso di wine, d'ora in poi qualsiasi comando lanciato relativo
 # a wine utilizzerà questa directory
```

```sh
 echo $WINEPREFIX 
 # verifichiamo di aver settato bene il wine 
 # prefix
```

```sh
 env WINEPREFIX=~/.wine-mioProgramma wineboot -u
```

Possiamo anche utilizzare delle alternative grafiche a wineboot, 
che ci permettono anche di configurare già l'ambiente windows 
creato, questi comandi sono:

```sh
 wine winecfg 
 # apre un tool grafico di configurazione per 
 # creare un ambiente windows con le impostazioni selezionate
```

Altre applicazioni utili che possono essere avviate sono:

```sh
 wine control 
 # apre il pannello di controllo, facoltativo per 
 # la fase di setup
```

```sh
 wine uninstaller 
 # apre il menu di installazione/disinstallazione programmi, 
 # facoltativo per la fase di setup
```

```sh
 wine regedit 
 # apre l'editor del registro di sistema, facoltativo per la fase di setup
```

```sh
 wine iexplore # apre internet explorer
```

```sh
 wine console # apre una console windows
```

Di default Wine salva i suoi file di configurazione e i programmi installati
nella directory ~/.wine. Questa directory e' comunemente chiamata "Wine Prefix"
o "Wine Bottle" e viene automaticamente creata/aggiornata ogniqualvolta
eseguiamo un programma windows o uno dei tool di configurazione distribuiti con
Wine, come ad esempio `winecfg`.
Una directory di wine prefix contiene anche una struttura di directory uguale
a quella comunemente presente sui sistemi Windows, quindi possiamo trovare 
infatti la directory C:, cioe' il cosiddetto "C drive".

Di default su un sistema a 64 bit, wine si avvierà in modalità 64 
bit, possiamo cambiare questo comportamento andando a settare la 
variabile “WINEARCH=win32” in questo modo le applicazioni avviate 
verranno lanciate in modalità di compatibilità 32 bit.

Una volta settato il Wine Prefix possiamo lanciare il nostro 
programma windows eseguendo:

```sh
 wine percorso/al/nomeProgramma.exe 
 # esegue un programma Windows
```
oppure:

```sh
 wine c:\\myapps\\nomeProgramma.exe 
 # esegue un programma Windows usando i percorsi in stile Windows
```
se questo non dovesse avere successo possiamo eseguire:

```sh
 WINEDEBUG=err+all wine nomeProgramma.exe 
 # in questo caso, vediamo tutti gli errori di wine, questo ci permetterà di 
 # eseguire un debug e mettere a posto i problemi
```

```sh
 WINEDEBUG=+loaddll 
 # ci permette di capire quali librerie DLL vengono usate e qualora vengano 
 # caricate come “builtin” o “ native”.
```
molte volte gli errori sono dovuti a dipendenze mancanti, 
possiamo installarle manualmente oppure installare “winetricks” 
che ci aiuterà ad installarle, ad esempio se il programma “
mio.exe” da un errore dicendo che la libreria “mfc42.dll” è 
mancante, possiamo eseguire:

```sh
 winetricks mfc42 
 # installa la libreria mfc42 nell'ambiente Windows
```
Per altri problemi può essere utile anche controllare la versione 
installata con:

```sh
 wine --version
```
```sh
 winetricks --version
```

N.B.: Mai e poi mai eseguire wine da utente root

## Variabili d'Ambiente

### La variabile d'ambiente WINEDEBUG

Per rendere più facile le operazioni di troubleshooting
dei bug, Wine fornisce diversi livelli di verbosita' per i messaggi di debug 
chiamati "debug channel".

Ogni canale di debug, quando attivato, attiverà la registrazione
dei messaggi da mostrare alla console in cui wine e' stato invocato.
Alcuni canali di debug possono generare grossi volumi di log. 
Per filtrare i log generati e' utile controllare la chiave di registro
chiamata "RelayExclude" che gestisce i trace reports.
relay trace reports). 
E' da considerare che la registrazione dei messaggi di debug rallenta 
l'esecuzione di Wine, quindi e' consigliabile non utilizzare WINEDEBUG 
a meno che non si desideri realmente fare troubleshooting.

All'interno di ciascun canale di debug, è possibile specificare 
ulteriormente una classe di messaggi specifici, per filtrare le 
diverse gravità di errore. Le quattro classi di messaggi sono:

* trace 
* fixme
* warn 
* err 

Per attivare un canale di debug si utilizza la forma "class+channel".
Per disattivare un canale di debug invece si puo' utilizzare 
la forma "class-channel". 
E' possibile anche attivare piu' canali di debug o disattivarne piu'
di uno, per fare questo e' sufficiente utilizzare la virgola per dividere
le opzioni.To list more than one channel in the 
same WINEDEBUG option, separate them with commas. 
Vediamo alcuni esempi di settaggio della variabile WINEDEBUG:

```sh
 WINEDEBUG=warn+heap wine nomeProgramma.exe 
 # mostra i messaggi della classe warn, del canale di debug heap
```
```sh
 WINEDEBUG=heap wine program_name 
 # mostra i messaggi del canale 
 # heap, di tutte e quattro le classi
```
```sh
 WINEDEBUG=+all,-relay 
 # mostra i messaggi di tutti i canali, 
 # eccetto il canale “relay”
```
###  La variabile d'ambiente WINEDLLOVERRIDES

Non è sempre possibile runnare applicazioni con librerie DLL 
builtin di wine, a volte semplicemente le librerie native 
funzionano meglio, queste impostazioni possono essere gestite via 
GUI da “winecfg”, ma possiamo regolarle anche con la variabile 
d'ambiente “WINEDLLOVERRIDES”. Vediamo alcuni esempi:

```sh
 WINEDLLOVERRIDES="comdlg32,shell32=n,b" 
 # carica le librerie 
 # comdlg32 e shell32 come native “n”, se il caricamento da native 
 # dovesse fallire allora le carica come builtin “b”
```
```sh
 WINEDLLOVERRIDES="comdlg32=b,n;shell32=b;comctl32=n;oleaut32=" 
 # carica la libreria comdlg32 prima come builtin e poi come 
 # native se il caricamento della builtin fallisce, poi carica la 
 # libreria shell32 come builtin, la libreria comctl32 come 
 # nativa, e disabilita la libreria oleaut32
```

###  DLL Overrides

Le librerie DLL vengono comunemente caricate nel seguente ordine:

1. The directory the program was started from.
2. The current directory.
3. The Windows system directory.
4. The Windows directory.
5. The PATH variable directories. 
 
###  La variabile d'ambiente WINEARCH

La variabile d'ambiente WINEARCH specifica l'architettura Windows 
da supportare, a volte infatti ci può capitare lanciando un 
programma con wine di incappare nell'errore “Bad Exe Format”, 
questo molto probabilmente è un problema dovuto al settaggio di 
questa variabile, di default su un sistema a 64 bit, questa 
variabile anche se non impostata è di default impostata per 
un'architettura a 64 bit, possiamo cambiarla così:

```sh
 WINEARCH=win32
```
```sh
 WINEARCH=win64
```
se un ambiente è stato creato con una determinata architettura 
wine si rifiuterà di runnare qualsiasi cosa da quell'ambiente se 
cambiamo architettura.

###  Variabili d'Ambiente Windows/DOS

Possiamo impostare normalmente una variabile d'ambiente da 
GNU/Linux queste verranno importate tutte da Wine nell'ambiente 
Windows, quindi valgono le stesse regole che valgono per le 
variabili d'ambiente GNU/Linux.

###  Avviare Wine

Possiamo avviare wine in diversi modi, ad esempio:

```sh
 wine nomeProgramma.exe
```
```sh
 wineconsole 
 # avvia una console di wine (Windows)  
```
Nota che wineconsole puo' essere avviato in due modalita' diverse:

  * --backend=user 
    # apre un'altra finestra terminale per 
    # wineconsole

  * --backend=curses 
     # apre wineconsole nella stessa finestra in 
     # cui è lanciato, con un'interfaccia curses

possiamo avviare un programma utilizzando wineconsole con:
```sh
 wineconsole nomeProgramma.exe
```
## Hardware in Wine

### CD/DVD e dispositivi di memoria vari


Per montare lettori CD o altri dispositivi di memoria è molto 
semplice, infatti ci basta linkare la directory del dispositivo 
di memoria a wine, in questo modo:

```sh
 ln -s /mnt/cdrom ~/.winePrefixDirectory/dosdevices/d: 
 # in questo modo l'istanza di wine con WINEPREFIX specificato, vedrà 
 # il cdrom come drive “d:”
```

### Porte Seriali e Parallele


La configurazione di porte seriali e parallele è molto simile 
alla configurazione dei “drive” di memoria, cioè ci basterà 
creare un link simbolico in “~/.winePrefixDirectory/dosdevices” 
col nome del device. In windows i device seriali hanno nomi come “
com1”, “com2”, ecc... mentre i device paralleli vanno con “lpt1”, 
“lpt2”, ecc... Ad esempio per configurare una porta seriale e una 
porta parallela una volta posizionati nella directory “
~/.winePrefixDirectory/dosdevices” eseguiamo:

```sh
 ln -s /dev/ttyS0 com1
```
```sh
 ln -s /devlp0 lpt1
```

### Network Shares

Le condivisioni di rete sono mappate all'interno della directory “
unc/” quindi qualsiasi programma che prova ad accedere al 
percorso “\\myserver\some\file” cercherà in “
~/.wine/dosdevices/unc/myserver/some/file”, ad esempio nel caso 
avessimo usato Samba per montare “\\myserver\some” su “
/mnt/smb/myserver/some”, una volta posizionati all'interno di “
~/.wine/dosdevices” allora possiamo eseguire:

```sh
 ln -s /mnt/smb/myserver/some unc/myserver/some
```

### Stampanti

Wine può interagire direttamente col sistema CUPS installato 
localmente, se non c'è CUPS, viene utilizzato il vecchio sistema 
di printing BSD-Printing, tutte le stampanti elencate in 
/etc/printcap sono installate automaticamente in Wine, Wine ha 
bisogno di un file PPD per ogni stampante anche se un file 
chiamato “generic.ppd” è installato con Wine; possiamo usare il 
comando “lpr” per stampare un documento all'interno di Wine.

### Scanner

In windows, gli scanner usano le API TWAIN per accedere 
all'hardware, le API TWAIN builtin di wine, semplicemente 
forwardano le richieste alle librerie SANE di Linux; quindi prima 
di utilizzare SANE, dobbiamo prima assicurarci di poter accedere 
allo scanner interessato attraverso SANE; inoltre assicuriamoci 
di avere a disposizione il programma “xscanimage” utilizzato per 
scansionare un'immagine; normalmente questo è contenuto nel 
pacchetto sane-frontends.

## Configurazioni Varie

### Font


Font configuration, once a nasty problem, is now much simpler. If 
you have a collection of TrueType fonts in Windows it's simply a 
matter of copying the .ttf files into c:\windows\fonts. 

### Winetricks

Winetricks is a script to allow one to install base requirements 
needed to run Windows programs. Installable components include 
DirectX 9.x, MSXML (required by Microsoft Office 2007 and 
Internet Explorer), Visual Runtime libraries and many more. 

Per elencare le categorie di tool che possiamo installare con 
winetricks eseguiamo:

```sh
 winetricks list
```
una volta vista la lista delle categorie possiamo ad esempio 
visualizzare le possibilità di installazione che abbiamo 
all'interno di una categoria con:

```sh
 winetricks nomeCategoria list #elenca le possibili 
  installazioni per ogni categoria
```
```sh
 winetricks list-all #elenca tutte le possibili installazioni
```
possiamo verificare i moduli installati eseguendo:

```sh
 winetricks list-installed #elenca i moduli installati
```
## Registro di Sistema di Windows

Tutte le impostazioni settate con “winecfg” fatta eccezione per 
le configurazioni sui drive montati, sono salvate nel registro di 
sistema, in Windows questo registro è un repository centrale per 
la configurazione del sistema operativo e delle varie 
applicazioni.

### Struttura del Registro di Sistema di Windows


Il registro di sistema di Windows è molto molto complesso, qui ci 
limiteremo alle basi, quello che dobbiamo conoscere è:

* HKEY_LOCAL_MACHINE:
  -- Questa è una root key fondamentale (in win9x è salvato nel 
    file nascosto system.dat), questa voce contiene tutto quello 
    pertinente alla corrente installazione di Windows, ed è 
    spesso abbreviata col nome HKLM

* HKEY_USERS:
  -- Questa è una root key fondamentale (in win9x è salvato nel 
    file nascosto user.dat) contiene dati relativi agli utenti 
    della corrente installazione di Windows 

* HKEY_CLASSES_ROOT:
  -- Questo è un link a HKEY_LOCAL_MACHINE\Software\Classes. 
    Contiene dati relativi ad associazioni programma/file, OLE 
    Document Handlers, e COM classes.

* HKEY_CURRENT_USER:
  -- Questo è un link a HKEY_USERS\your_username, ergo, la 
    configurazione di un utente specifico.

Queste informazioni sono salvate nei seguenti file:
* system.reg
  -- Questo file contiene HKEY_LOCAL_MACHINE

* user.reg
  -- Questo file contiene HKEY_CURRENT_USER

* userdef.reg
  -- Questo file contiene HKEY_USERS\.Default (ergo, le 
    impostazioni default a livello utente)

Non è consigliabile editare con editor di testo questi file, ma è 
consigliato accedere alle impostazioni attraverso il tool “
regedit”, con “wine regedit”; una voce di particolare interesse 
per gli utenti Wine è “HKEY_CURRENT_USER\Software\Wine”, in 
questa area del registro vengono memorizzate tutti i settaggi 
impostati da “winecfg”.

## Troubleshooting

Cosa posso provare per risolvere problemi con un programma 
specifico ?

* controllare che il giusto WINEPREFIX sia utilizzato
* verificare la configurazione del wine prefix con “winecfg”
* cambiare versione di Windows da “winecfg”, a volte cambiare versione 
  di windows può risolvere problemi
* controllare i problemi sulle librerie attivando i messaggi di debug attraverso
  -- WINEDEBUG=+loaddll wine percorso/al/programma.exe
  oppure in alternativa con:
  -- WINEDEBUG=err+all wine percorso/al/programma.exe
* usare diverse modalità grafiche
  -- questo è ottenibile attraverso il cambio di impostazioni 
    attraverso “winecfg”, da qui possiamo selezionare tra la 
    modalità “Desktop” e la modalità “normal managed”, questa 
    configurazione può effetivamente fare la differenza

### Alcuni Problemi Riscontrati


#### wine-gecko

In alcune versioni di wine, come ad esempio wine per alcune 
distro (e.g. Debian) non ci installa un'istanza di mono (versione 
open-source di .NET) per ogni prefix appena creato, questo ci può 
dare dei problemi, ad esempio potrebbe non farci caricare pagine 
eseguendo “wine iexplore”, l'errore in console sarà qualcosa del 
tipo “Could not load wine-gecko. HTML rendering will be disabled”,
per risolvere questo problema è consigliabile utilizzare la 
versione di wine fornita dal sito con i propri repository.

