
# Pràctiques de SI amb el Thinkbook

Propostes de treball amb els alumnes al mòdul de Seguretat Informàtica amb els nous portàtils que arriben al centre.

Son dotacions de centre per fer servir pel professorat del Departament d'Educació de la Generalitat de Catalunya.

Aquests ordinadors arribaran al nostre centre el 14/12/2020 amb:

  * BIOS/UEFI configurada per no arrencar des de dispositius externs

  * Contrasenya desconeguda a la BIOS/UEFI

  * Disc dur xifrat amb BitLocker

  * Pot ser que els privilegis de l'usuari que ens donin no siguin administratius

Què volem fer amb els alumnes? Pràctiques de SI forense sobre un cas real, que serà el portàtil que m'assignin.

  * Trobar la contrasenya de la BIOS/UEFI

  * Trobar la contrasenya de BitLocker

  * Trobar la contrasenya d'Administrador.

  * Com instal·lar un nou sistema malgrat les proteccions i després restaurar l'original.

Pregunta: **és legal el que vull fer?** A veure que ens fan signar quan ens lliurin el portàtil.



## Informació dels equips

Equip: Lenovo ThinkBook 14 IIL Intel Core i3-1005G1/8GB/256GB SSD/14"

Serial: LR0E2Q4P

Model: sembla que 20SL00D3SP però a la pàgina de suport he trobat que és 20SLS0S700

TPM: 2.0

PDF amb les especificacions del model ThinkBook 14 IIL:
<https://psref.lenovo.com/syspool/Sys/PDF/ThinkBook/ThinkBook_14_IIL/ThinkBook_14_IIL_Spec.PDF>

Pàgina d'informació del producte:  
<https://www.tiendalenovo.es/lenovo-thinkbook-14-iil-20sl00d3sp>

Pàgina de suport del producte per descàrregues (noves BIOS, etc.):  
<https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkbook-series/thinkbook-14-iil/20sl/20sls0s700/lr0e2q4p>

Manual del hardware per muntatge / desmuntatge:  
<https://download.lenovo.com/consumer/mobiles_pub/thinkbook14_15_hmm_20191106.pdf>



## Atacs a la contrasenya BIOS / UEFI

 1. Provar contrasenyes típiques: password, passw0rd, p4ssw0rd, 12345678, 11111111, etc. De quina longitud pot ser la contrasenya?

 2. Recerca sobre si el fabricant té un backdoor per quan un client oblida perd contrasenya: sembla que no

 3. És un portàtil -> La contrasenya no s'hauria d'esborrar traient bateria i piles. Estarà gravada a una EEPROM.

 4. És un portàtil de marca -> La contrasenya no s'hauria d'esborrar descarregant una nova BIOS/UEFI i gravant-la.

    El fitxer de la BIOS a descarregar està a https://download.lenovo.com/consumer/mobiles/djcn22ww.exe

    Gravar una nova BIOS podria fer que BitLocker es queixi, especialment si es reseteja algun paràmetre de la BIOS del TPM:

    <https://support.lenovo.com/us/en/solutions/ht506425>

 5. Quin atac d'enginyeria social faries servir per que el tècnic que fa el manteniment et proporcioni la contrasenya?



## Atacs a la contrasenya BitLocker

Es tracta de la versió automàtica de BitLocker: no requereix una contrasenya per part de l'usuari, ni un usb amb una clau.

... i tenim accés al hardware ...

Primer fes una mica de recerca de com funciona [BitLocker](https://en.wikipedia.org/wiki/BitLocker) i de què és el [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module).

  <https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview>

  <https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-bitlocker>

Cal tenir clar que a un determinat moment de l'inici del sistema la contrasenya es mourà dels xips de l'equip (TPM) a memòria RAM per poder desxifrar el volum.

 0. Podem obtenir una mica d'informació de les metadades de BitLocker, per exemple amb la comanda: `dislocker-metadata`

    <http://manpages.ubuntu.com/manpages/bionic/man1/dislocker-metadata.1.html>

 1. Hotmail/Live/Outlook/Microsoft webmail: la clau BitLocker quan xifrem un volum es mou al compte de Microsoft, i es pot recuperar d'allà a l'enllaç <https://account.microsoft.com/devices/recoverykey>

    En aquest cas no funcionarà per que qui va xifrar aquesta unitat no vam ser nosaltres.

 2. Forense: buscar contrasenyes als fitxers de paginació i d'hibernació.

 3. Atac Cold Boot: anàlisi forense de la RAM d'un equip a la recerca de contrasenyes.  Molt senzill però espectacular!!!!  Els alumnes fliparan.

    <https://citp.princeton.edu/our-work/memory/code/>

    <https://en.wikipedia.org/wiki/Cold_boot_attack>

    <https://www.bleepingcomputer.com/news/security/cold-boot-attack-steals-passwords-in-under-two-minutes/>

    <https://www.youtube.com/watch?v=oWDpP8ThGP8>

    Per crear l'usb d'arrencada consultar <https://www.rmprepusb.com/tutorials/124>

    L'espray congelador es pot comprar a botigues d'electrònica o per Internet:

    <https://www.google.com/search?q=freeze+spray+electronic>

 4. Atac de esnifar la clau del TPM. Cal comprar hardware especialitzat tot i que econòmic, i ser una mica manetes de l'electrònica.

    <https://pulsesecurity.co.nz/articles/TPM-sniffing>

    <https://www.youtube.com/watch?v=-Fj3SeZww3M>

 5. Atac de força bruta i de contrasenya sobre el volum. Si trobo la contrasenya provar si també és la de la BIOS.

    <https://github.com/e-ago/bitcracker>

    Aquest atac és fàcil i pot motivar els estudiants si el faig a un equip amb targetes gràfiques nVidia amb CUDA.



## Atacs per aconseguir privilegis d'Administrador

 1. Buscar contrasenya amb [l0phtcrack](https://www.l0phtcrack.com/). Si no tenim permisos per llegir les contrasenyes del registre, sempre podem aconseguir una còpia de seguretat del fitxer SAM que es guarda a c:/windows/system32/config o quelcom semblant.

 2. Si s'ha aconseguit arrencar amb CD/USB es pot provar [ophcrack](https://ophcrack.sourceforge.io/) + RainbowTables per obtenir la contrasenya

 3. Si s'ha aconseguit arrencar amb CD/USB es pot provar [chntpw](https://en.wikipedia.org/wiki/Chntpw) per canviar la contrasenya 

 4. Com segur tenim accés com usuaris sense privilegis, podem intentar atacs d'escalada de privilegis a Windows.

Work in progress



## Canviar / restaurar S.O. malgrat proteccions

Work in progress

Amb el disc dur xifrat (BitLocker), necessitarem clonar per sectors, no per fitxers -> `dd`
