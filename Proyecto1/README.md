*Universidad de San Carlos de Guatemala*  
*Facultad de Ingenieria*  
*Escuela de Ciencias y Sistemas*  
*Redes De Computadoras 1*  
*Segundo Semestre 2024.*  

___
## **Practica 1**
**201900087 - José Manuel Lacán Chavajay**  
**201908355 - Danny Hugo Bryan Tejaxún Pichiyá**  

## 1. Detalle de Direcciones IP y VLAN

### VLAN
Suma últimos dígitos de carnet: `7 + 5 = 12`  
Último dígito de suma: `2`  

| Departamento | VLAN | ID RED |
| - | - | - |
| Contabilidad | `22` | `192.168.22.0/24`|
| Secretaría | `32` | `192.168.32.0/24`|
| RRHH | `42` | `192.168.42.0/24`|
| IT | `52` | `192.168.52.0/24`|

### Contabilidad IP
| Equipo | IP |
| - | - |
| S_CONTABILIDAD | `192.168.22.1` |
| CONTABILIDAD_1 | `192.168.22.2` |
| CONTABILIDAD_2 | `192.168.22.3` |

### Secretaría IP
| Equipo | IP |
| - | - |
| SECRETARIA1 | `192.168.32.1` |
| SECRETARIA2 | `192.168.32.2` |
| SECRETARIA | `192.168.32.3` |

### RRHH IP
| Equipo | IP |
| - | - |
| S_RRHH | `192.168.42.1` |
| RRHH_1 | `192.168.42.2` |
| RRHH_2 | `192.168.42.3` |
| RRHH | `192.168.42.4` |

### IT IP
| Equipo | IP |
| - | - |
| S_IT | `192.168.52.1` |
| IT_1 | `192.168.52.2` |
| IT_2 | `192.168.52.3` |

## 2. Implementación de la Topología

La red propuesta consta de tres secciones:
* Backbone
* Centro Administrativo
* Área de Trabajo

[![Topologia.png](https://i.postimg.cc/JnqnkgG8/Topologia.png)](https://postimg.cc/YGvMcd6D)

### Backbone
Esta área se encarga de dar redundancia y conectividad entre todos los departamentos y sus servidores. En esta red se localiza tanto el servidor VTP como la raíz del STP.

[![Backbone.png](https://i.postimg.cc/LXPmRWMR/Backbone.png)](https://postimg.cc/2bCs726X)

### Centro Administrativo
En esta área se ubica el espacio destinado la administración principal para cada departamento.

[![Centro-Administrativo.png](https://i.postimg.cc/Rhz40TMt/Centro-Administrativo.png)](https://postimg.cc/CBJWPknM)

### Área de Trabajo
En esta área se encuentran todos los dispositivos físicos de cada departamento.

[![Area-De-Trabajo.png](https://i.postimg.cc/bvszVJCZ/Area-De-Trabajo.png)](https://postimg.cc/KKhSRZyb)


## 3. Detalle de los Comandos Usados

### 3.1 Configuración de Modo Troncal 
```bash
# Para SW1, SW2, SW3, SW4, SW5, SW6, SW7, SW8, SW9, SW10, SW11, SW12 y SW13.
enable
configure terminal
interface range f0/rango
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan all
exit
exit
wr

nota: el rango cambiaba dependiendo cuantas interfaces conectadas tenian los switch.

# Para S1, S2, S3, S4, S5, S6, S7, S8.
enable
configure terminal
interface f0/1
switchport mode trunk
switchport trunk allowed vlan all
exit
exit
wr
```

### 3.2 Configuración de VLANs
```bash
enable
configure terminal
vlan 22
name Contabilidad
exit
vlan 32
name Secretaria
exit
vlan 42
name RRHH
exit
vlan 52
name IT
exit
exit
wr
show vlan
```

### 3.3 Configuración de VTP
```bash
# Configuración Servidor
## Para SW1
enable
configure terminal
vtp mode server
vtp domain P15
vtp password usac
vtp version 2
exit
wr
show vtp status

# Configuración Modo Cliente
## Para SW2, SW3, SW4, SW5, SW6, SW7, SW8, SW10, SW11, SW12, SW13, S1, S2, S3, S4, S5, S6, S7 y S8.
enable
configure terminal
vtp mode client
vtp domain P15
vtp password usac
exit
wr
show vtp status

#Configuración Modo Transparente
## Para SW9.
enable
configure terminal
vtp mode transparent
vtp domain P15
vtp password usac
end
show vtp status
```

### 3.4 Configuración de STP
```bash
# Configuración de SW1 como Root Bridge
enable
configure terminal
spanning-tree vlan 1 root primary
spanning-tree vlan 22 root primary
spanning-tree vlan 32 root primary
spanning-tree vlan 42 root primary
spanning-tree vlan 52 root primary
exit
wr
show spanning-tree

# Configuración de RSTP-rapid pvst
enable
configure terminal
spanning-tree mode rapid-pvst
exit
wr
show spanning-tree
```

### 3.5 Configuración de Modo Acceso
```bash
#Modo acceso a Contabilidad
enable
configure terminal
interface fa0/11
switchport mode access
switchport Access vlan 22
exit
exit
wr
show interface status

#Modo acceso a Secretaria
enable
configure terminal
interface fa0/11
switchport mode access
switchport Access vlan 32
exit
exit
wr
show interface status

#Modo acceso a RRHH
enable
configure terminal
interface fa0/11
switchport mode access
switchport Access vlan 42
exit
exit
wr
show interface status

#Modo acceso a IT
enable
configure terminal
interface fa0/11
switchport mode access
switchport Access vlan 52
exit
exit
wr
show interface status
```

## 4. Ping entre Hosts
### 4.1 Ping entre CENTRO_ADMINISTRATIVO Y AREA DE TRABAJO
[![1.png](https://i.postimg.cc/tRttXRb9/1.png)](https://postimg.cc/SjRzdhjP)
> Hubo respuesta ya que ambas tienen acceso a la misma VLAN.

### 4.2 PING ENTRE BACKBONE Y CENTRO_ADMINISTRATIVO
[![2.png](https://i.postimg.cc/nhYt2Db6/2.png)](https://postimg.cc/MnT4KXGD)
> Hubo respuesta ya que ambas pertenecen a la misma VLAN.
