# Memòria del Projecte

**Defensa Corporativa: NDR + SIEM + Resposta a Incidents**
Especialització en Ciberseguretat — ASIR

> **Què s'explica en aquest document?** Visió general del projecte: objectius, arquitectura de la solució, tecnologies utilitzades, metodologia de treball i resultats de les proves de validació.

---

## 1. Resum executiu

Aquest projecte presenta el disseny i desplegament d'una arquitectura de defensa corporativa basada en un SOC intern amb SIEM, monitorització i resposta a incidents, utilitzant exclusivament eines Open Source.

S'ha construït una xarxa segmentada amb firewall OPNsense, un SIEM centralitzat amb Wazuh, monitorització amb Grafana i Prometheus, i un sistema de detecció d'intrusions amb Suricata integrat al SIEM. S'han desplegat serveis corporatius: web (Apache), correu (Postfix + Dovecot + Roundcube), FTP (vsftpd) i base de dades (MariaDB). També s'ha instal·lat un honeypot Cowrie per atraure atacants potencials.

Finalment, s'ha habilitat un entorn de proves amb una màquina atacant que simula amenaces reals (phishing, escaneig de ports i força bruta SSH) per verificar que el sistema de detecció respon en menys de 30 segons.

---

## 2. Objectius

- Segmentar la xarxa corporativa amb VLANs (LAN, DMZ, WiFi) controlades per OPNsense
- Implementar VPN segures per a administradors (WireGuard) i usuaris remots (OpenVPN)
- Desplegar serveis corporatius: web, correu, FTP i base de dades
- Instal·lar un SOC amb Wazuh (SIEM) i Grafana + Prometheus (monitorització)
- Integrar tots els hosts de la LAN amb el SIEM
- Simular atacs controlats per validar la detecció i generació d'alertes

---

## 3. Arquitectura de la solució

L'arquitectura es compon de:

- **Firewall OPNsense** amb segmentació per VLANs i polítiques de tràfic entre zones
- **DMZ** amb servidor web exposat i honeypot Cowrie
- **LAN corporativa** amb tots els serveis interns i el SOC
- **VPN d'administrador** (WireGuard) amb accés complet a la infraestructura
- **VPN d'usuari** (OpenVPN) amb accés limitat als recursos necessaris
- **Xarxes WiFi** corporatiu i de convidats, sense accés a la LAN

Les polítiques de firewall asseguren que:
- La DMZ no pot iniciar connexions cap a la LAN
- El WiFi no pot accedir a la LAN
- Les VPNs tenen accés segons el perfil de l'usuari

---

## 4. Tecnologies utilitzades

| Tecnologia | Versió | Funció |
|---|---|---|
| OPNsense | - | Firewall, segmentació, IDS/IPS |
| Wazuh | 4.x | SIEM (Manager, Indexer, Dashboard) |
| Suricata | 8.0.3 | IDS/IPS |
| Prometheus | 2.53.5 | Mètriques i alertes |
| Grafana | 13.0.1 | Visualització i dashboards |
| WireGuard | - | VPN administradors |
| OpenVPN | - | VPN usuaris |
| Apache | 2.4.66 | Servidor web |
| Roundcube | - | Webmail |
| Postfix | - | Servidor SMTP |
| Dovecot | - | Servidor IMAP |
| vsftpd | 3.0.5 | Servidor FTP |
| MariaDB | 10.11 | Base de dades |
| Cowrie | - | Honeypot SSH |
| Docker | - | Contenidor per al honeypot |
| Nmap | - | Escaneig de ports (proves) |
| Hydra | - | Força bruta (proves) |

---

## 5. Metodologia

El projecte s'ha desenvolupat en 5 fases en 13 setmanes:

| Fase | Durada |
|---|---|
| Anàlisi i disseny | 2 setmanes |
| Preparació de l'entorn (maquinari, cablejat) | 3 setmanes |
| Desplegament de serveis i seguretat | 4 setmanes |
| Proves de validació amb atacs simulats | 2 setmanes |
| Documentació i tancament | 2 setmanes |

---

## 6. Proves de validació

S'ha configurat una màquina atacant amb Nmap i Hydra per simular tres amenaces:

| Atac | Eina | Detecció |
|---|---|---|
| Phishing | Correu maliciós simulat | Alerta Wazuh |
| Escaneig de ports | Nmap | Alerta Suricata + Wazuh |
| Força bruta SSH | Hydra + RockYou | Alerta Wazuh (nivell 10) |

Totes les amenaces es detecten en **menys de 30 segons** i generen alertes automàtiques al dashboard de Wazuh.

---
