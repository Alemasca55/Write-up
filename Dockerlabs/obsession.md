# Write-up Obsesion
> Maquina de El pingüino de Mario-Fecha de creación: 25/06/2024 https://dockerlabs.es/

> Fecha de creación del Write-up 20/02/2025

# TODO LO QUE SE REALIZA EN ESTE DOCUMENTO ES CON FINES EDUCATIVOS, NO ME HAGO RESPONSABLE DEL USO INDEBIDO DE ESTA INFORMACIÓN.



## Índice
1. [Descripción](#Descripción)
2. [Herramientas](#Herramaientas)
3. [Instalación](#Instalación)
4. [Proceso_de_explotación](#Proceso_de_explotación)

## Descripción

Durante todo el proceso del **Write-up** se demostrará con las diferentes herramientas cómo se puede llegar a entrar al modo **root** y poder dar por acabada la prueba de intrusión.

## Herramientas

| Herramientas     | Descripción                       | Versión    |
|------------------|-----------------------------------|-----------|
|**Nmap**| Permite descubrir dispositivos en la red, servicios, puertos...           | 7.94SVN  |
| **Metasploit**   | Permite a través de diferentes componentes de la herramienta buscar exploits para poder aprovecharse de las vulnerabilidades que existan.      | 6.4.34-dev|
| **Dirb**   | Permite a través de diferentes componentes de la herramienta buscar exploits para poder aprovecharse de las vulnerabilidades que existan.      | 6.4.34-dev|
| **Ftp**| Permite conectarse al servicio de ftp de la máquina.      | 20230507-2+b1|
| **ssh**   | Permite a través de diferentes componentes de la herramienta buscar exploits para poder aprovecharse de las vulnerabilidades que existan.      | 6.4.34-dev|
| **Hydra**   | Permite a través de diferentes componentes de la herramienta buscar exploits para poder aprovecharse de las vulnerabilidades que existan.      | 6.4.34-dev|

## Instalación

Antes de empezar deberemos desplegar la máquina vulnerable para que esté disponible durante todo el proceso de intrusión.
```bash

#Le damos permisos de ejecución.

└─$ sudo chmod +x auto_deploy.sh 
[sudo] contraseña para alema: 
                                                                             
┌──(alema㉿alema)-[~/Escritorio/dockerlabs/Muy_facil/obsession]
└─$ sudo bash auto_deploy.sh obsession.tar 

                            ##        .         
                      ## ## ##       ==         
                   ## ## ## ##      ===         
               /""""""""""""""""\___/ ===       
          ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
               \______ o          __/           
                 \    \        __/            
                  \____\______/               
                                          
  ___  ____ ____ _  _ ____ ____ _    ____ ___  ____ 
  |  \ |  | |    |_/  |___ |__/ |    |__| |__] [__  
  |__/ |__| |___ | \_ |___ |  \ |___ |  | |__] ___] 
                                         
                                     

Estamos desplegando la máquina vulnerable, espere un momento.

Máquina desplegada, su dirección IP es --> 172.17.0.2

Presiona Ctrl+C cuando termines con la máquina para eliminarla


```
## Proceso_de_explotación

Ahora que la máquina ya esta disponible, pasaremos a comprobar qué servicios y puertos tiene abiertos a la red.

```
#Deberemos de utilizar la herramienta Nmap con el parámetro -A para que compruebe puertos,servicios,scripts...

┌──(alema㉿alema)-[~/Escritorio/dockerlabs/Muy_facil/obsession]
└─$ sudo nmap -A 172.17.0.2 
[sudo] contraseña para alema: 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-02-20 12:25 CET
Nmap scan report for 172.17.0.2
Host is up (0.000077s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0             667 Jun 18  2024 chat-gonza.txt
|_-rw-r--r--    1 0        0             315 Jun 18  2024 pendientes.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:172.17.0.1
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 1
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 60:05:bd:a9:97:27:a5:ad:46:53:82:15:dd:d5:7a:dd (ECDSA)
|_  256 0e:07:e6:d4:3b:63:4e:77:62:0f:1a:17:69:91:85:ef (ED25519)
80/tcp open  http    Apache httpd 2.4.58 ((Ubuntu))
|_http-title: Russoski Coaching
|_http-server-header: Apache/2.4.58 (Ubuntu)
MAC Address: 02:42:AC:11:00:02 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE
HOP RTT     ADDRESS
1   0.08 ms 172.17.0.2

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.04 seconds


```

Tras hacer el escaneo, se puede observar que la máquina  tiene el servicio de ftp con la versión de vsftpd 3.0.5.Dentro de este,se puede ver que hay dos txt accesibles por el usuario Anonymous.

```

#Nos conectaremos al servicio con el usuario Anonymous.

                                                                                                              
┌──(kali㉿kali)-[~]
└─$ ftp 172.17.0.2                                                                  
Connected to 172.17.0.2.
220 (vsFTPd 3.0.5)
Name (172.17.0.2:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
#Comprobamos los archivos disponibles.
ftp> ls
229 Entering Extended Passive Mode (|||10120|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             667 Jun 18  2024 chat-gonza.txt
-rw-r--r--    1 0        0             315 Jun 18  2024 pendientes.txt
226 Directory send OK.
#Descargamos los archivos disponibles.
ftp> mget *.txt
mget chat-gonza.txt [anpqy?]? 
229 Entering Extended Passive Mode (|||27876|)
150 Opening BINARY mode data connection for chat-gonza.txt (667 bytes).
100% |***********************************************************************************************************************************************************************************************|   667      227.90 KiB/s    00:00 ETA
226 Transfer complete.
667 bytes received in 00:00 (84.86 KiB/s)
mget pendientes.txt [anpqy?]? 
229 Entering Extended Passive Mode (|||13375|)
150 Opening BINARY mode data connection for pendientes.txt (315 bytes).
100% |***********************************************************************************************************************************************************************************************|   315      271.26 KiB/s    00:00 ETA
226 Transfer complete.
315 bytes received in 00:00 (157.10 KiB/s)
ftp> exit
221 Goodbye.

````
#Comprobamos el contenido de los archivos.                                                                                                                                                                                                                                           
```bash

┌──(kali㉿kali)-[~]
└─$ cat chat-gonza.txt
[16:21, 16/6/2024] Gonza: pero en serio es tan guapa esa tal Nágore como dices?
[16:28, 16/6/2024] Russoski: es una auténtica princesa pff, le he hecho hasta un vídeo y todo, lo tengo ya subido y tengo la URL guardada
[16:29, 16/6/2024] Russoski: en mi ordenador en una ruta segura, ahora cuando quedemos te lo muestro si quieres
[21:52, 16/6/2024] Gonza: buah la verdad tenías razón eh, es hermosa esa chica, del 9 no baja
[21:53, 16/6/2024] Gonza: por cierto buen entreno el de hoy en el gym, noto los brazos bastante hinchados, así sí
[22:36, 16/6/2024] Russoski: te lo dije, ya sabes que yo tengo buenos gustos para estas cosas xD, y sí buen training hoy
                                                                                                                                                                                                                 
┌──(kali㉿kali)-[~]
└─$ cat pendientes.txt 
1 Comprar el Voucher de la certificación eJPTv2 cuanto antes!

2 Aumentar el precio de mis asesorías online en la Web!

3 Terminar mi laboratorio vulnerable para la plataforma Dockerlabs!

4 Cambiar algunas configuraciones de mi equipo, creo que tengo ciertos
  permisos habilitados que no son del todo seguros...

```
#Una vez hemos revisado el servicio ftp,vamos a revisar el contenido que tiene por el puerto 80.

![image](https://github.com/user-attachments/assets/e70bc3fc-cb0b-4303-a204-c1bd9e22fe4e)


Como podemos ver hay una pagína web con contenido,para poder comprobar si tiene algún tipo de archivo desprotregido,algun subdominio o alguna pista que nos sea útil utilizaremos el siguiente comando:

```bash  
┌──(kali㉿kali)-[~]
└─$ dirb http://172.17.0.2   


-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Feb 13 16:04:59 2025
URL_BASE: http://172.17.0.2/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://172.17.0.2/ ----
==> DIRECTORY: http://172.17.0.2/backup/                                                                                                                                                                                                   
==> DIRECTORY: http://172.17.0.2/important/                                                                                                                                                                                                
+ http://172.17.0.2/index.html (CODE:200|SIZE:5208)                                                                                                                                                                                        
+ http://172.17.0.2/server-status (CODE:403|SIZE:275)                                                                                                                                                                                      
                                                                                                                                                                                                                                           
---- Entering directory: http://172.17.0.2/backup/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                                                                                                                                                                                           
---- Entering directory: http://172.17.0.2/important/ ----
(!) WARNING: Directory IS LISTABLE. No need to scan it.                        
    (Use mode '-w' if you want to scan it anyway)
                                                                               
-----------------
END_TIME: Thu Feb 13 16:05:01 2025
DOWNLOADED: 4612 - FOUND: 2

```
#Si revisamos el contenido del comando dirb podemos ver que nos ha listado dos directorios,"backup" y "important".Al comprobar el contenido como indica la herramienta,podemos ver lo siguiente:

##En el directorio "http://172.17.0.2/important/" 



Con todo esto se da por acabado el objetivo principal de la máquina.




