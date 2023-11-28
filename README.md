# Balanceador De Carga Con Apache

## Índice

1. [Introducción](#introducción)
2. [Objetivo](#objetivo)
3. [Infraestructura](#infraestructura)
4. [Configuración Paso a Paso](#configuración-paso-a-paso)
 - 4.1 [Pasos previos a la creacion de instancias](#Pasos-previos-a-la-creacion-de-instancias)
 - 
   -4.1.1 [Crear una VPC](#Crear-una-VPC)
        -4.1.2 [Configurar grupos de seguridad](#Configurar-grupos-de-seguridad)
        -4.1.3 [Configurar ACL](#Configurar-ACL) 
 - 4.2 [Configuración de la Infraestructura en AWS](#configuración-de-la-infraestructura-en-aws)
 - 
     - 4.2.1 [Capa 1: Balanceador de Carga](#capa-1-balanceador-de-carga)
     - 4.2.2 [Capa 2: Servidores Web](#capa-2-servidores-web)
     - 4.2.3 [Capa 3: Servidor de Base de Datos](#capa-3-servidor-de-base-de-datos)
    - 4.3 [Configuración de Certificado SSL](#configuración-de-certificado-ssl)
    - 4.6 [Despliegue de la Aplicación de Gestión de Usuarios](#despliegue-de-la-aplicación-de-gestión-de-usuarios)
    - 4.7 [Opcional: Despliegue de CMS](#opcional-despliegue-de-cms)
5. [Conclusión](#Conclusión)

## Introducción

El propósito de esta práctica es configurar una infraestructura en AWS que incluya un servidor balanceador para distribuir peticiones a los servidores web en el back-end y a un servidor de datos que albergará un servidor MySQL. La aplicación web se implementará en los servidores back-end utilizando Apache y PHP, conectándose al servidor MySQL que ejecuta MariaDB.

## Objetivo

La infraestructura mejorará la seguridad al permitir que solo el balanceador tenga acceso a Internet.

## Infraestructura

La infraestructura de este proyecto se trata de un balanceador en capa 1 expuesta a red pública con dos servidor web en Backend y capa 2 (red privada) ademas de un servidor de BBDD en capa 3 (red privada).

## Configuración Paso a Paso

### Pasos previos a la creacion de instancias

### Crear una VPC

En la consola de AWS buscamos VPC y creamos una VPC nueva.
![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/2370997f-f926-4b77-9e58-e9de429004a8)
Elegimos VPC y MÁS por el hecho que nos facilita la creacion de subredes públicas y privadas.

### Configurar grupos de seguridad

Despues de crear la VPC creamos un grupo de seguridad dirigiéndonos al panel de búsqueda poniendo grupos de seguridad, allí crearemos 3 grupos de seguridad correspondientes a las 3 capas

Colocamos el nombre y una descripcion, importante elegir la VPC que hemos creado previamente.
En la capa 1 nos interesa en la regla de entrada habilitar HTTP (Puerto 80) y HTTPS (Puerto 443):
![image](https://github.com/ArturoLucero28/BalanceadorDeCargaConApache/assets/146435794/456b3e12-578d-4a29-b32c-aa40fa8f1ef7)
(hay que colocar sus respectivas reglas de salida)
En la capa 2  colocamos las mismas reglas


### Configurar ACL

### Configuración de la Infraestructura en AWS

-Crear las maquinas virtuales en "EC2".

#### Capa 1: Balanceador de Carga

En la capa 1 necesitaremos 1 instancia

#### Capa 2: Servidores Web

En la capa 2 necesitaremos 2 instancias

#### Capa 3: Servidor de Base de Datos

En la capa 3 necesitaremos una instancia

### Configuración de Certificado SSL

...

## Conclusión



