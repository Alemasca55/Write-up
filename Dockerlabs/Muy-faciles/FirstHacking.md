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

