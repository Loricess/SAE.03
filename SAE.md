### **Adresse IP :**

**Réseaux**: 
- 172.15.96.0/22 → Utilisé pour la zone client .
- 172.15.100.0/22 → Utilisé pour la zone personnel.
- 172.9.8.0/22  → Utilisé pour la zone serveurs.
- 192.69.9.0/24 → Passerelle R <-> R_S
- 192.19.8.0/24 → Passerelle R <-> R_P
- 192.12.11.0/24 → Passerelle R <-> R_C

Plage DHCP de 172.15.96.1 à 172.15.97.195

  
**Adresse IP** : 
Zone personnelle ( 1024 adresses possible ) :
- PCA : 172.15.100.1/22.
- PCB : 172.15.100.2/22.
- R_P eth 0 : 172.15.101.254/22.
- R_P eth 1 : 192.19.8.1/24.  

Zone client ( 1024 adresses possible ) :
- R_C eth 0 : 172.15.99.254/22.
- R_C eth 1 : 192.12.11.1/24.
- PCC : adresses dynamique.
- PCD : adresses dynamique.
  
Zone serveurs: ( 1024 adresses possible)  
Réseaux 1 : 172.9.8.0/23 ( 512 )
- Sweb : 172.9.8.1/23.
- R_S eth 0 : 172.9.9.254/23.
- R_S eth 1 : 192.69.9.1/24.
- R_D eth 1: 172.9.9.253/23.

Réseaux 2 : 172.9.10.0/23 ( 512 ) 
- SF : 172.9.10.1/23.
- SDemo : 172.9.10.2/23.
- SAdmin : 172.9.10.3/23.
- R_D eth 0: 172.9.11.254/23.

Routeur R :
- R eth 1 vers R_S : 192.69.9.2/24.
- R eth 2 vers R_P : 192.19.8.2/24.
- R eth 3 vers R_C : 192.12.11.2/24

### **Commande**

Zone personnelle : 

```
// PCA
ip route add default via 172.15.101.254

// PCB
ip route add default via 172.15.101.254

// R_P
ip route add default via 192.19.8.1

// R_P vers Zone serveurs
ip route add 192.9.8.0 via 192.19.8.2
ip route add 192.9.10.0 via 192.19.8.2

```

Zone client: 

```
// PCC
ip route add default via 172.15.99.254

// PCB
ip route add default via 172.15.99.254

// R_C
ip route add default via 192.12.11.1
```
  
Zone serveur: 172.9.8.0/22

```
// Sweb
ip route add default via 172.9.11.254
ip route add 172.9.10.0 via 172.9.9.254  
  
// SF
ip route add default via 172.9.11.254

// SAdmin
ip route add default via 172.9.11.254

// SDemo
ip route add default via 172.9.11.254

// R_D
ip route add default via 172.9.9.254
(ip route add 192.19.8.0 via 172.9.11.254)  

// R_S 
ip route add default via 192.19.8.1
ip route add 192.9.10.0 via 172.9.9.253
ip route add 172.15.100.0 via 192.69.2  
  
```


DHCP:

  

ddns-update-style none ;

subnet 172.15.96.0 netmask 255.255.252.0

{

range 172.15.96.1 172.15.97.195;

default-lease time 21600;

max lease time 43200;

option router 172.15.99.254;

}

Route : 

// Routeur principal (R)

  

// Vers la zone client depuis la zone serveurs

ip route add 172.15.96.0/22 via 192.12.11.1

  

// Vers la zone serveurs depuis la zone client

ip route add 172.9.8.0/23 via 192.69.9.1

ip route add 172.9.10.0/23 via 192.69.9.1

  
  

// Routeur R_S (serveurs)

  

// Vers la zone client via R

ip route add 172.15.96.0/22 via 192.69.9.2

  
  

// Routeur R_C (client)

  

// Vers la zone serveurs via R

ip route add 172.9.8.0/23 via 192.12.11.2

ip route add 172.9.10.0/23 via 192.12.11.2

  
  
**