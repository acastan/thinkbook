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
 
 Pregunta: és legal el que vull fer?



## Informació dels equips

Equip: Lenovo ThinkBook 14 IIL Intel Core i3-1005G1/8GB/256GB SSD/14"

Serial: LR0E2Q4P

Model: sembla que 20SL00D3SP però a la pàgina de suport he trobat que és 20SLS0S700

Pàgina d'informació del producte:  
<https://www.tiendalenovo.es/lenovo-thinkbook-14-iil-20sl00d3sp>

Pàgina de suport del producte per descàrregues (noves BIOS, etc.):
<https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkbook-series/thinkbook-14-iil/20sl/20sls0s700/lr0e2q4p>

Manual del hardware per muntatge / desmuntatge:
<https://download.lenovo.com/consumer/mobiles_pub/thinkbook14_15_hmm_20191106.pdf>



## Contrasenya BIOS / UEFI

 1. Provar contrasenyes típiques: password, passw0rd, p4ssw0rd, 12345678, 11111111, etc. De quina longitud pot ser la contrasenya?
 
 2. Recerca sobre si el fabricant té un backdoor per quan un client oblida perd contrassenya: sembla que no
 
 3. És un portàtil -> La contrasenya no s'hauria de recuperar treient bateria i pilas. Estarà gravada a una EEPROM.
 
 4. És un portàtil de marca -> La contrasenya no s'hauria d'esborrar descarregant una nova BIOS/UEFI i gravant-la.
 
    El fitxer de la BIOS a descarregar està a https://download.lenovo.com/consumer/mobiles/djcn22ww.exe
    
    Gravar una nova BIOS podria fer que BitLocker es queixi, especialment si es reseteja algún paràmetre de la BIOS del TPM.
 
 5. Quin atac d'enginyeria social faries servir per que el tècnic que fa elmanteniment et proporcioni la contrasenya?
 


## BitLocker

 * <https://support.lenovo.com/us/en/solutions/ht506425>
 
 * <https://docs.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-bitlocker>
 
 * <https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview>
 
 * <http://manpages.ubuntu.com/manpages/bionic/man1/dislocker-metadata.1.html>
 
 * <https://www.linuxjournal.com/magazine/cold-boot-attack-tools-linux>
