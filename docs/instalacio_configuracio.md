# Manual d'Instal·lació i Configuració

> **Què s'explica en aquest document?** Procediment general d'instal·lació i configuració de cada servei desplegat: OPNsense, Apache, Roundcube, Wazuh + Suricata, vsftpd, Grafana + Prometheus, Cowrie, MariaDB, Postfix + Dovecot i la màquina d'atacs. No inclou configuracions internes detallades ni credencials.

---

## 1. OPNsense

Es va instal·lar OPNsense en un equip dedicat amb 4 interfícies de xarxa (WAN, LAN, DMZ, WiFi). 

**Configuració aplicada:**
- DHCP actiu per assignar IPs automàticament a la LAN
- DNS amb Unbound per a la resolució de noms locals
- Suricata com a IDS/IPS per detectar trànsit maliciós
- Regles de firewall per segmentar el tràfic entre VLANs
- NAT per als serveis exposats a la DMZ

---

## 2. Apache

Instal·lat en Fedora mitjançant el gestor de paquets `dnf`. Es va configurar un VirtualHost per al domini local amb HTTPS mitjançant certificat autosignat. Es va obrir el firewall per als ports 80 i 443.

---

## 3. Roundcube (webmail)

Instal·lat en Fedora juntament amb Apache, MariaDB, PHP i les extensions necessàries. Es va crear la base de dades per a Roundcube i es va configurar l'accés web.

**Accés:** http://ip-del-servidor/roundcubemail/

---

## 4. Wazuh + Suricata (SIEM)

**Servidor Wazuh** instal·lat en Ubuntu Server amb els quatre components:
- Wazuh Manager (correlació d'esdeveniments)
- Wazuh Indexer (emmagatzematge basat en OpenSearch)
- Wazuh Dashboard (interfície web)
- Filebeat (recol·lector de logs)

**Suricata** instal·lat com a IDS/IPS al mateix servidor. Es va integrar amb Wazuh configurant-lo per llegir el fitxer de logs de Suricata (`eve.json`). D'aquesta manera, les alertes de xarxa es visualitzen directament al dashboard de Wazuh.

**Agents Wazuh** instal·lats a cada màquina Fedora de la LAN, configurats per enviar logs al servidor central.

**Protecció:** firewall local UFW configurat per permetre només els ports necessaris (SSH, Wazuh Dashboard, comunicació amb agents i API).

---

## 5. vsftpd (FTP)

Instal·lat en Fedora. Es va configurar per permetre només usuaris locals (sense accés anònim) amb permisos d'escriptura. Es va obrir el port 21 al firewall.

---

## 6. Grafana + Prometheus

Instal·lats en Ubuntu Server. Prometheus recull mètriques del sistema (Node Exporter) i realitza sondes de xarxa (Blackbox Exporter) per verificar l'estat dels hosts i serveis de la LAN. Grafana proporciona els dashboards de visualització.

**Dashboards desplegats:**
- Node Exporter Full: mètriques del servidor (CPU, RAM, disc)
- Blackbox Exporter: monitorització HTTP/HTTPS
- LAN Completa: estat de tots els hosts de la xarxa
- Prometheus Overview: estat intern de Prometheus

**Alertes configurades:** CPU > 80%, RAM > 85%, disc > 85%, host no accessible.

---

## 7. Cowrie (Honeypot)

Desplegat amb Docker i docker-compose. Consta de:
- **Cowrie**: honeypot SSH al port 2222
- **Dashboard web**: visualització d'intents de connexió i comandes executades
- **Nginx**: servidor web per al dashboard

Es va configurar OPNsense per redirigir el tràfic del port 22 de la WAN cap al port 2222 del honeypot, simulant un servei SSH real.

---

## 8. MariaDB

Instal·lat en Fedora. Es va executar l'script de seguretat per eliminar usuaris anònims i desactivar l'accés remot de root. Es van crear usuaris amb diferents rols (administrador, analista, lector) i permisos granulars.

**phpMyAdmin** instal·lat per facilitar la gestió gràfica de la base de dades.

---

## 9. Postfix + Dovecot

Instal·lats en Fedora. Postfix configurat com a servidor SMTP per a l'enviament de correu, i Dovecot com a servidor IMAP per a la recepció. Les bústies s'emmagatzemen en format Maildir.

---

## 10. Màquina atacant (proves)

Màquina Fedora amb Nmap, Hydra i la wordlist RockYou. Utilitzada per:
- Escanejar ports i serveis de la LAN
- Realitzar atacs de força bruta SSH i FTP
- Verificar que el SIEM detecta i alerta correctament

---

## 11. WiFi

Router TP-Link configurat amb dues xarxes WiFi separades (corporatiu i convidats), amb accés restringit exclusivament a Internet.
