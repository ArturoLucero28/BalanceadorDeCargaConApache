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
        - 4.2.1 [Capa 1: Balanceador de Carga](#capa-1:-balanceador-de-carga)
        - 4.2.2 [Capa 2: Servidores Web](#capa-2:-servidores-web)
        - 4.2.3 [Capa 3: Servidor de Base de Datos](#capa-3:-servidor-de-base-de-datos)
    - 4.3 [Configuración de Certificado SSL](#configuración-de-certificado-ssl)
    - 4.6 [Despliegue de la Aplicación de Gestión de Usuarios](#despliegue-de-la-aplicación-de-gestión-de-usuarios)
    - 4.7 [Opcional: Despliegue de CMS](#opcional-despliegue-de-cms)
5. [Conclusión](#Conclusión)

## Introducción

En este manual, desarrollaremos un balanceador de carga con apache en aws.

## Objetivo

El objetivo del proyecto es implementar una infraestructura en la nube utilizando AWS
para crear un entorno de máquinas virtuales organizado en tres capas. 

## Infraestructura

La infraestructura de este proyecto es un balanceador en capa 1 expuesta a red pública con dos servidor web en Backend y capa 2 (red privada) ademas de un servidor de BBDD en capa 3 (red privada).

## Configuración Paso a Paso

### 4.1 Pasos previos a la creacion de instancias

### 4.1.1 Crear una VPC

### 4.1.2 Configurar grupos de seguridad

### 4.1.3 Configurar ACL

### 4.2 Configuración de la Infraestructura en AWS

-Crear las maquinas virtuales en "EC2".

#### 4.2.1 Capa 1: Balanceador de Carga

En la capa 1 necesitaremos 1 instancia

#### 4.2.2 Capa 2: Servidores Web

En la capa 2 necesitaremos 2 instancias

#### 4.2.3 Capa 3: Servidor de Base de Datos

En la capa 3 necesitaremos una instancia

### 4.3 Configuración de Certificado SSL

...

## Conclusión



