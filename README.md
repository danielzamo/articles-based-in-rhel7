---
author: Daniel Oscar Zamo
date: 29/06/2022
title: README.md
tag: Linux, RHEL7, networking
---

## Configurando interface gestión de red

## Supuestos cumplidos

- Salvo que se indique expresamente, los comandos a continuación son realizados sobre un Linux basado en RHEL7. Es una instalación básica/mínima y tiene todos los paquetes actualizados a lo último estable en la versión 7.
- La salida del comando `nmcli d` es:
```bash
nmcli d
DEVICE  TYPE      STATE         CONNECTION         
eth0    ethernet  connected     Wired connection 1 
eth1    ethernet  disconnected  --                 
eth2    ethernet  disconnected  --                 
lo      loopback  unmanaged     -- 
```
De modo que para el primer caso de prueba, primero se borra 

### Especificación

- Hostname: nfs-centos7
- IP: 192.168.123.99/24
- Gateway: 192.168.123.1
- DNS: 80.58.61.250, 80.58.61.254
- name port gestion: gestion
- device gestion: eth0

```bash
domain='.my-asus.net'
name='nfs-centos7'
ip='192.168.123.99/24'
gateway='192.168.123.1'
dns='80.58.61.250,80.58.61.254'
nameGestion='gestion'
deviceGestion='eth0'

# Seteo nombre de hostname
sudo hostnamectl set-hostname ${name}${domain}
# Reviso nombre de los dispositivos
nmcli d
# BEGIN: Cambio nombre de la conección de "eth0" (se da por supuesto que es inicialmente este nombre) a "gestion" (ver referencia)
sudo nmcli connection modify "${deviceGestion}" 802-3-ethernet.mac-address "$(ip link show ${deviceGestion}|tail -1|awk '{print $2}')"
# Se cambia de nombre a la conección ejecutando
sudo nmcli connection modify "${deviceGestion}" connection.interface-name "${nameGestion}"
sudo shutdown -r now
# Se puede verificar ejecutando:
ip link
# // END: Cambio nombre ...
# Modificando configuración de la interface
sudo nmcli c modify ${deviceGestion} autoconnect yes con-name ${nameGestion} ifname ${deviceGestion} ipv4.addresses ${ip} ipv4.method manual ipv4.gateway ${gateway} ipv4.dns ${dns}
sudo shutdown -r now
```

## Otros comandos útiles

```bash
nmcli device show
nmcli d
nmcli d s

ip l
ip a
ip r

hostnamectl
```

## Referencias

- [How to Set a Custom Interface Name with NetworkManager in CentOS/RHEL 7](https://www.thegeekdiary.com/how-to-set-a-custom-interface-name-with-networkmanager-in-centos-rhel-7/)
