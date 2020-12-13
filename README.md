# Pràctiques de SI amb el Thinkbook

Propostes de treball amb els alumnes al mòdul de Seguretat Informàtica amb els nous portàtils que arriben al centre.

Són dotacions de centre per fer servir pel professorat del Departament d'Educació de la Generalitat de Catalunya.

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

Pàgina d'informació del producte:  
<https://www.tiendalenovo.es/lenovo-thinkbook-14-iil-20sl00d3sp>

Pàgina de suport del producte per descàrregues (noves BIOS, etc.):  
<https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkbook-series/thinkbook-14-iil/20sl/20sls0s700/lr0e2q4p>

Manual del hardware per muntatge / desmuntatge:  
<https://download.lenovo.com/consumer/mobiles_pub/thinkbook14_15_hmm_20191106.pdf>



## Atacs a la contrasenya BIOS / UEFI

 1. De quina longitud pot ser la contrasenya? 8? 6?

    Provar contrasenyes típiques de 8 caràcters: password, passw0rd, p4ssw0rd, 12345678, 11111111, etc.

    Provar contrasenyes típiques de 6 caràcters: passwd, 123456, 000000, admin0, admin1, etc.

 2. Recerca sobre si el fabricant té un backdoor per quan un client oblida perd contrasenya. A la web de Lenovo diuen que no.

 3. És un portàtil -> La contrasenya no s'hauria d'esborrar traient bateria i piles. Estarà gravada a una EEPROM.

 4. És un portàtil de marca -> La contrasenya no s'hauria d'esborrar descarregant una nova BIOS/UEFI i flashejant-la.

    El fitxer de la BIOS a descarregar està a https://download.lenovo.com/consumer/mobiles/djcn22ww.exe

    Gravar una nova BIOS podria fer que BitLocker es queixi, especialment si es reseteja algun paràmetre de la BIOS del TPM:

    <https://support.lenovo.com/us/en/solutions/ht506425>
    
 5. Les EEPROM es poden resetejar fent un pont entre unes potes determinades, però cal tenir molt clar quines potes són, que depenen del model de xip EEPROM. Si ens equivoquem de potes, cremarem l'EEPROM.

    Per exemple, veure aquest vídeo: <https://www.youtube.com/watch?v=nFW_F0ZDESk>

    I aquests: <https://www.youtube.com/results?search_query=lenovo+eeprom>

 6. Quin atac d'enginyeria social faries servir per que el tècnic que fa el manteniment et proporcioni la contrasenya?



## Atacs a la contrasenya BitLocker

Es tracta de la versió automàtica de BitLocker: no requereix una contrasenya per part de l'usuari, ni un usb amb una clau ... i tenim accés al hardware !

Primer fes una mica de recerca de com funciona [BitLocker](https://en.wikipedia.org/wiki/BitLocker) i de què és el [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module).

  <https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview>

  <https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-bitlocker>

Cal tenir clar que a un determinat moment de l'inici del sistema la contrasenya es mourà dels xips de l'equip (TPM) a memòria RAM per poder desxifrar el volum.

 0. Podem obtenir una mica d'informació de les metadades de BitLocker, per exemple amb la comanda: `dislocker-metadata`

    <http://manpages.ubuntu.com/manpages/bionic/man1/dislocker-metadata.1.html>

 1. Hotmail/Live/Outlook/Microsoft webmail: la clau BitLocker quan xifrem un volum Windows es mou al nostre compte de Microsoft, i es pot recuperar d'allà a l'enllaç <https://account.microsoft.com/devices/recoverykey>

    Però en aquest cas no funcionarà per que qui va xifrar aquesta unitat no vam ser nosaltres.

 2. Atac de força bruta i de contrasenya sobre el volum. Si trobo la contrasenya provar si també és la de la BIOS.

    <https://github.com/e-ago/bitcracker>

    Aquest atac és fàcil i pot motivar els estudiants si el faig a un equip amb targetes gràfiques nVidia amb CUDA.
    
    L'atac de contrasenya és viable, però crec que l'atac de força bruta no ho és per que l'espai de claus és massa gran, i tardaríem anys en trobar-la.

 3. Forense: buscar contrasenyes als fitxers de paginació i d'hibernació.

 4. Atac Cold Boot: anàlisi forense de la RAM d'un equip a la recerca de contrasenyes.  Molt senzill però espectacular!!!!  Els alumnes fliparan.

    <https://citp.princeton.edu/our-work/memory/code/>

    <https://en.wikipedia.org/wiki/Cold_boot_attack>

    <https://www.bleepingcomputer.com/news/security/cold-boot-attack-steals-passwords-in-under-two-minutes/>

    <https://www.youtube.com/watch?v=oWDpP8ThGP8>

    Per crear l'usb d'arrencada consultar <https://www.rmprepusb.com/tutorials/124>

    L'esprai congelador es pot comprar a botigues d'electrònica o per Internet:

    <https://www.google.com/search?q=freeze+spray+electronic>

 5. Atac de esnifar la clau del TPM. Cal comprar hardware especialitzat, tot i que econòmic, i ser una mica manetes de l'electrònica.

    <https://pulsesecurity.co.nz/articles/TPM-sniffing>

    <https://www.youtube.com/watch?v=-Fj3SeZww3M>



## Atacs per aconseguir privilegis d'Administrador

 1. Buscar contrasenya amb [l0phtcrack](https://www.l0phtcrack.com/). Si no tenim permisos per llegir les contrasenyes del registre, sempre podem aconseguir una còpia de seguretat del fitxer SAM que Windows guarda a c:/windows/system32/config .

 2. Si s'ha aconseguit arrencar amb CD/USB es pot provar [ophcrack](https://ophcrack.sourceforge.io/) + RainbowTables per obtenir la contrasenya

 3. Si s'ha aconseguit arrencar amb CD/USB es pot provar [chntpw](https://en.wikipedia.org/wiki/Chntpw) per canviar la contrasenya 

 4. Com segur tenim accés com usuaris sense privilegis, podem intentar atacs d'escalada de privilegis a Windows.

 5. Explorar les polítiques de seguretat no ens donarà accés d'administrador, però és interessant veure què no ens deixen fer:

    executar 'gpedit.msc' -> Configuració d'equip -> Configuració de seguretat -> Polítiques de control d'aplicacions -> AppLocker -> explora i esborra les regles -> a continuació executar 'gpupdate /force'



## Canviar / restaurar S.O. malgrat proteccions

Avís: Amb el disc dur xifrat (BitLocker), necessitarem clonar el Windows per sectors, no per fitxers -> `dd`

Avís: Sembla que el trackpad només funciona amb nuclis de Linux prou nous, del 5.6 cap amunt.

Avís: El nucli de Linux ha d'estar signat per a que SecureBoot el deixi arrencar.

Avís: El sistema operatiu ve a una unitat NVMe, però l'equip té espai per posar un altre disc dur SATA.

Avís: tecla F12 deixa escollir medi d'arrencada, si la BIOS està configurada per permetre-ho.

 1. Sempre es pot treure el disc NVMe de l'equip, posar-lo en un altre equip amb UEFI i SecureBoot, gravar el sistema que volem, i retornar-lo a l'equip original.

 2. Sembla que pot funcionar escollir al panell de configuració de Windows que utilitzi un usb com a mitjà de recuperació, i a continuació fer apagades "a la brava" per forçar l'arrencada des d'usb.

 3. També podem treure les bateries durant un minut per resetejar les opcions de la BIOS. Per exemple:

    <https://www.youtube.com/watch?v=76I2Ec9K7lw>



## Altres

Una mica de pensament lateral: quins arxius van usar i després esborrar abans de distribuir la imatge? Hi ha a la unitat encara informació esborrada del compte que es va fer servir per xifrar el disc dur? -> Podem emprar un recuperador d'arxius per veure tot el que s'ha esborrat al disc dur. Podem fer un anàlisi forense de la unitat. 

Quan em donin el PC, abans d'arrencar el Windows cal fer una còpia amb `dd` de tot el disc dur.

Work in progress
