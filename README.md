# Pràctiques de Seguretat Informàtica amb el Thinkbook

Propostes d'exercici d'anàlisi forense i hacking inspirat en els nous portàtils que arriben als instituts. Aquests portàtils són dotacions de centre per fer servir pel professorat del Departament d'Educació de la Generalitat de Catalunya.

Aquests ordinadors arriben al nostre centre el 14/12/2020 amb:

  * BIOS/UEFI configurada per no arrencar des de dispositius externs.

  * Contrasenya desconeguda a la BIOS/UEFI.

  * Disc dur xifrat amb BitLocker.

  * Els privilegis de l'usuari que ens donen no són administratius, impossibilitant d'instal·lar i executar segons quin programari.

Què volem fer amb els alumnes? Pràctiques de recerca d'informació a ASIX-M11-SI-UF1 sobre forense amb un cas similar, podent fer alguna prova o comprovació sobre el cas real, que serà el portàtil que m'assignin.

  * Trobar la contrasenya de la BIOS/UEFI

  * Trobar la contrasenya de BitLocker

  * Escalar privilegis o trobar/eliminar la contrasenya d'Administrador.

  * Com instal·lar un nou sistema operatiu malgrat les proteccions i després restaurar l'original.

Pregunta: **és legal el que vull fer?** A mi no m'han fet signar cap full ni m'han donat cap instrucció del que podem i no podem fer amb l'equip.



## Recerca d'informació dels equips

Equip: Lenovo ThinkBook 14 IIL Intel Core i3-1005G1/8GB/256GB SSD/14"

Serial: LR0E2Q4P

Model: sembla que pugui ser 20SL00D3SP però a la pàgina de suport he trobat que és 20SLS0S700

TPM: 2.0

PDF amb les especificacions del model ThinkBook 14 IIL:
<https://psref.lenovo.com/syspool/Sys/PDF/ThinkBook/ThinkBook_14_IIL/ThinkBook_14_IIL_Spec.PDF>

Pàgina d'informació del producte:  
<https://www.tiendalenovo.es/lenovo-thinkbook-14-iil-20sl00d3sp>

Pàgina de suport del producte per descàrregues (noves BIOS, etc.):  
<https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkbook-series/thinkbook-14-iil/20sl/20sls0s700/lr0e2q4p>

Manual del hardware per muntatge / desmuntatge:  
<https://download.lenovo.com/consumer/mobiles_pub/thinkbook14_15_hmm_20191106.pdf>

Posaran documentació de Xtec aquí:  
<https://projectes.xtec.cat/edc/documentacio/>

Sistema operatiu: sembla que és un Windows 10 Pro Education:
<https://en.wikipedia.org/wiki/Windows_10_editions#Comparison_chart>
<https://support.microsoft.com/es-es/help/3181976/windows-10-editions-for-education-customers>



## Atacs a la contrasenya BIOS / UEFI

 1. De quina longitud pot ser la contrasenya? 8? 6?

    Provar contrasenyes típiques de 8 caràcters: password, passw0rd, p4ssw0rd, 12345678, 11111111, etc.

    Provar contrasenyes típiques de 6 caràcters: passwd, 123456, 000000, admin0, admin1, etc.
    
    Solució: Finalment la contrasenya és de 12 caràcters (-ja és coneguda i fins on jo sé ja està circulant per la xarxa, perquè a algun centre els tècnics que venien a repartir els portàtils en un moment de cansament o aburriment la van donar al professorat per que es posessin ells mateixos la contrasenya al portàtil-). No es tracta de cap contrasenya típica, tanmateix sí segueix un patró esperat: paraula seguida de un número d'entre dos i quatre xifres, i la paraula amb la clàssica substitució de vocals per dígits (a/4 , e/3 , i/1, o/0). Concretament una paraula de 8 caràcters i un número de 4 xifres. El número de quatre xifres és, com la majoria de vegades, un any.

 2. Recerca sobre si el fabricant té un backdoor per quan un client oblida perd contrasenya.

    Solució: A la web de Lenovo diuen que no.

 3. Es pot esborrar la contrasenya de la BIOS traient les bateries, com a una torre?

    Solució: És un portàtil i la contrasenya NO s'hauria d'esborrar traient bateria i piles, ja que als portàtils està gravada a una memòria externa anomenada EEPROM que no requereix alimentació.

 4. Es pot esborrar la contrasenya de la BIOS descarregant una nova BIOS/UEFI i flashejant-la?

    El fitxer de la BIOS a descarregar està a https://download.lenovo.com/consumer/mobiles/djcn22ww.exe

    Gravar una nova BIOS podria fer que BitLocker es queixi, especialment si es reseteja algun paràmetre de la BIOS del TPM: <https://support.lenovo.com/us/en/solutions/ht506425>

    Solució: Ho ha provat una altre escola i diuen que no. En un portàtil de marca la contrasenya no s'hauria d'esborrar amb aquest mètode.

 5. Les EEPROM es poden resetejar fent un pont entre unes potes determinades, però cal tenir molt clar quines potes són, que depenen del model de xip EEPROM. Si ens equivoquem de potes, cremarem l'EEPROM.

    Per exemple, veure aquest vídeo: <https://www.youtube.com/watch?v=nFW_F0ZDESk>

    I aquests: <https://www.youtube.com/results?search_query=lenovo+eeprom>

 6. Quin atac d'enginyeria social faries servir per que el tècnic que fa el manteniment et proporcioni la contrasenya?
 
    Pista: quins moments pot estar distret? quins moments té més feina? és bon moment l'hora en que vol plegar de la feina?

Conclusions que haurien de treure els alumnes:

  * Per contrasenyes a la BIOS, no són viables els atacs de força bruta.

  * En els portàtils no serveixen les mateixes tècniques que a les torres. La contrasenya no s'esborra traient la bateria ni amb botó de reset.

  * En el cas d'anàlisi forense, no necessitem esbrinar o esborrar la contrasenya de la BIOS, ja que podem obrir l'equip i extreure el disc dur per a anàlisi.



## Atacs a la contrasenya BitLocker

Es tracta de la versió automàtica de BitLocker: no requereix una contrasenya per part de l'usuari, ni un usb amb una clau ... i a més tenim accés al hardware !

Primer fes una mica de recerca de com funciona [BitLocker](https://en.wikipedia.org/wiki/BitLocker) i de què és el [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module).

  <https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview>

  <https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-bitlocker>

Cal tenir clar que a un determinat moment de l'inici del sistema la contrasenya es mourà dels xips de l'equip (TPM) a memòria RAM per poder desxifrar el volum.

 0. Amb quines eines podem obtenir una mica d'informació de les metadades de BitLocker?

    Solució: Hi ha moltes. Per exemple amb la comanda: `dislocker-metadata`

    <http://manpages.ubuntu.com/manpages/bionic/man1/dislocker-metadata.1.html>

 1. La clau BitLocker quan xifrem un volum Windows es còpia al nostre compte de MicrosoftHotmail/Live/Outlook/Microsoft webmail. La podem recuperar d'allà?

    Solució: Tot i que normalment es pot recuperar d'allà a l'enllaç <https://account.microsoft.com/devices/recoverykey> , en aquest cas no funcionarà per que qui va xifrar aquesta unitat no vam ser nosaltres.

 2. És possible un atac de força bruta i de contrasenya sobre el volum?

    Solució: L'atac de contrasenya és viable, però crec que l'atac de força bruta no ho és per que l'espai de claus és massa gran, i tardaríem anys en trobar-la.

    Una eina pot ser <https://github.com/e-ago/bitcracker>

    Aquest atac és fàcil i pot motivar els estudiants si es fa amb un equip amb targetes gràfiques nVidia amb CUDA.

    Si trobem la contrasenya provar, podem mirar si també és la de la BIOS.

 3. És possible buscar contrasenyes als fitxers de paginació i d'hibernació?

    Solució: seria interessant, però com accedim a aquest fitxers? El fitxer de paginació ja existeix i el fitxer d'hivernació podem generar-lo posant l'equip en mode "hivernació". Però de moment si arrenquem amb el disc dur de l'equip potser no tenim privilegis, i si arrenquem amb un USB extern no tenim accés al contingut del disc perquè està xifrat. Així que cal provar si tenim permisos i, si no tenim, cal _veure al proper apartat com pujar privilegis_.
    
    > On-the-fly encryption keys (OTFE keys) are the actual binary keys that are used by the system to encrypt and decrypt information during normal operations. The keys are stored in the system’s volatile memory at all times while the encrypted volume is mounted to facilitate read/write access to encrypted data.
    > When the user puts their computer to sleep (as opposed to shutting it down), Windows default behavior is to save a copy of the device volatile memory on the computer’s hard drive so that the saved state could survive power cut off. The file that stores the content of the computer’s memory is called hibernation file. Windows stores hibernation files under the name “hiberfil.sys”. Hibernation files are encrypted; however, we were able to break this encryption.

 4. És possible un Atac Cold Boot (anàlisi forense de la RAM d'un equip a la recerca de contrasenyes)?

    Molt senzill però espectacular!!!!  Els alumnes fliparan.

    <https://citp.princeton.edu/our-work/memory/code/>

    <https://en.wikipedia.org/wiki/Cold_boot_attack>

    <https://www.bleepingcomputer.com/news/security/cold-boot-attack-steals-passwords-in-under-two-minutes/>

    <https://www.youtube.com/watch?v=oWDpP8ThGP8>

    Per crear l'usb d'arrencada consultar <https://www.rmprepusb.com/tutorials/124>

    L'esprai congelador es pot comprar a botigues d'electrònica o per Internet: <https://www.google.com/search?q=freeze+spray+electronic>

 5. És possible esnifar la clau del TPM?

    Cal comprar hardware especialitzat, tot i que econòmic, i ser una mica manetes de l'electrònica.

    <https://pulsesecurity.co.nz/articles/TPM-sniffing>

    <https://www.youtube.com/watch?v=-Fj3SeZww3M>

Conclusions que haurien de treure els alumnes:

  * Només l'atac de Cold Boot sembla viable si no tenim privilegis.



## Atacs per aconseguir privilegis d'Administrador

 1. Els usuaris i contrasenyes es guarden al registre, concretament al fitxer c:/windows/system32/config/SAM, i si tenim privilegis podem fer una còpia amb:

        reg save HKLM\SAM c:\sam

    Després podem buscar còmodament les contrassenyes d'aquest fitxer amb [l0phtcrack](https://www.l0phtcrack.com/).
    
    Si no tenim permisos per llegir les contrasenyes del registre, de vegades podem aconseguir una còpia de seguretat del fitxer SAM que Windows guarda a c:/windows/repair/SAM . Altrament, podem provar amb eines com [mimikatz](http://blog.gentilkiwi.com/mimikatz) amb els paràmetres `sekurlsa::logonpasswords`, [fgdump](http://swamp.foofus.net/fizzgig/fgdump/) , o [hashdump](https://www.offensive-security.com/metasploit-unleashed/meterpreter-basics/) .

    Solució: Encara per probar. <https://en.wikipedia.org/wiki/Security_Account_Manager#Cryptanalysis>

 2. Si s'ha aconseguit arrencar amb CD/USB es pot provar [ophcrack](https://ophcrack.sourceforge.io/) + RainbowTables per obtenir la contrasenya.

    Solució: En el nostre cas no es pot accedir al contingut del disc dur amb BitLocker actiu.

 3. Si s'ha aconseguit arrencar amb CD/USB es pot provar [chntpw](https://en.wikipedia.org/wiki/Chntpw) per canviar la contrasenya.

    Solució: En el nostre cas no es pot accedir al contingut del disc dur amb BitLocker actiu.

 4. Com segur tenim accés com usuaris sense privilegis, podem intentar atacs d'escalada de privilegis a Windows.

 5. Explorar les polítiques de seguretat no ens donarà accés d'administrador, però és interessant veure què no ens deixen fer:

    executar 'gpedit.msc' -> Configuració d'equip -> Configuració de seguretat -> Polítiques de control d'aplicacions -> AppLocker -> explora i esborra les regles -> a continuació executar 'gpupdate /force'

 6. Comenten companys/es d'altres instituts que han escalat privilegis de la següent manera: apagar malament el Windows i a continuació arrencar amb F12 i escollir mode recuperació permet iniciar sessió amb un usuari local administrador. Per consolidar aquest privilegi llavors podem crear un nou usuari administrador per iniciar sessió amb ell en el reinici.



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

Una mica de pensament lateral: quins arxius van usar i després esborrar abans de distribuir la imatge? Hi ha a la unitat encara informació esborrada del compte que es va fer servir per xifrar el disc dur? -> Podem emprar un recuperador d'arxius per veure tot el que s'ha esborrat al disc dur.
