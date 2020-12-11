# Pràctiques de SI amb el Thinkbook

Propostes de treball amb els alumnes al mòdul de Seguretat Informàtica amb els nous portàtils que arriben al centre.

Son dotacions de centre per fer servir pel professorat del Departament d'Educació de la Generalitat de Catalunya.

Aquests ordinadors arribaran al nostre centre el 14/12/2020 amb:

 * BIOS/UEFI configurada per no arrencar des de dispositius externs

 * Contrasenya desconeguda a la BIOS/UEFI

 * Disc dur xifrat amb Bitlocker

 * Pot ser que els privilegis de l'usuari que ens donin no siguin administratius

Què volem fer amb els alumnes? Pràctiques de SI forense sobre un cas real, que serà el portàtil que m'assignin.
 
  * Trobar la contrasenya de la BIOS/UEFI
  
  * Trobar la contrasenya de BitLocker
  
  * Trobar la contrasenya d'Administrador.
  
  * Com instal.lar un nou sistema malgrat les proteccions i després restaurar l'original.
 
 Pregunta: **és legal el que vull fer?**



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

 1. Provar contrasenyes típiques: password, passw0rd, p4ssw0rd, 12345678, 11111111, etc. De quina longitud pot ser la contrasenya?
 
 2. Recerca sobre si el fabricant té un backdoor per quan un client oblida perd contrassenya: sembla que no
 
 3. És un portàtil -> La contrasenya no s'hauria d'esborrar treient bateria i pilas. Estarà gravada a una EEPROM.
 
 4. És un portàtil de marca -> La contrasenya no s'hauria d'esborrar descarregant una nova BIOS/UEFI i gravant-la.
 
    El fitxer de la BIOS a descarregar està a https://download.lenovo.com/consumer/mobiles/djcn22ww.exe
    
    Gravar una nova BIOS podria fer que BitLocker es queixi, especialment si es reseteja algún paràmetre de la BIOS del TPM:
    
    <https://support.lenovo.com/us/en/solutions/ht506425>
 
 5. Quin atac d'enginyeria social faries servir per que el tècnic que fa elmanteniment et proporcioni la contrasenya?
 


## Atacs a la contrasenya BitLocker

Es tracta de la versió automàtica de BitLocker: no requereix una contrasenya per part de l'usuari, ni un usb amb una clau.

... i tenim accés al hardware ...

Primer fes una mica de recerca de com funciona [BitLocker](https://en.wikipedia.org/wiki/BitLocker) i de què és el [TPM](https://en.wikipedia.org/wiki/Trusted_Platform_Module).

  <https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview>
  
  <https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-bitlocker>

Cal tenir clar que a un determinat moment de l'inici del sistema la contrasenya es mourà dels xips de l'equip (TPM) a memia RAM per poderdesxifrar el volum.

 0. Podem obtenir una mica d'informació de les metadades de BitLocker, per exemple amb la comanda: `dislocker-metadata`
 
    <http://manpages.ubuntu.com/manpages/bionic/man1/dislocker-metadata.1.html>

 1. Hotmail: la clau BitLocker quan xifrem un volum es mou al compte de Hotmail, i es pot recuperar d'allà a l'enllaç
 
    En aquest cas no funcionarà per que qui va xifrar aquesta unitat no vam ser nosaltres.
 
 2. Forense: buscar contrasenyes als fitxers de paginació i d'hibernació.
 
 3. Atac Cold Boot: anàlisi forense de la RAM d'un equip a la recerca de contrasenyes.  Molt senzill però espectacular!!!!  Els alumnes fliparan.
 
    <https://citp.princeton.edu/our-work/memory/code/>
    
    <https://en.wikipedia.org/wiki/Cold_boot_attack>
    
    <https://www.bleepingcomputer.com/news/security/cold-boot-attack-steals-passwords-in-under-two-minutes/>
    
    <https://www.youtube.com/watch?v=oWDpP8ThGP8>
 
 4. Atac de esnifar la clau del TPM. Cal comprar hardware especialitzat tot i que econòmic, i ser una mica manetes de l'electrònica.

 5. Atac de força bruta i de contrasenya sobre el volum. Si trobo la contrasenya provar si també és la de la BIOS.



## Atacs per aconseguir contrasenya Administrador

Work in progress



## Cambiar / restaurar S.O. malgrat proteccions

Work in progress

Amb el disc dur xifrat (BitLocker), necessitarem clonar per sectors, no per fitxers -> `dd`
