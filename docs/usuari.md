# Manual d'Usuari

> **Què s'explica en aquest document?** Guia d'ús per als usuaris finals: accés al webmail, consulta d'alertes al SIEM, connexió FTP, visualització de dashboards de monitorització i accés a la base de dades via phpMyAdmin.

---

## 1. Accés al web (Apache)

Obrir un navegador i anar a la IP del servidor web.

Si no hi ha DNS configurat, afegir al fitxer `hosts` la IP del servidor amb el nom de domini corresponent.

---

## 2. Webmail (Roundcube)

**Accés:** `http://ip-del-servidor/roundcubemail/`

Iniciar sessió amb l'usuari i contrasenya del sistema.

### Enviar un correu
1. Clicar **Redacta** o **Nou missatge**
2. Omplir destinatari, assumpte i missatge
3. Clicar **Envia**

### Llegir un correu
1. Clicar a la carpeta **Entrada** (Inbox)
2. Clicar sobre el missatge per llegir-lo

### Configuració per a client extern (Thunderbird, Outlook)

| Paràmetre | Valor |
|---|---|
| Servidor IMAP | IP del servidor |
| Port IMAP | 143 |
| Servidor SMTP | IP del servidor |
| Port SMTP | 25 |
| Usuari | nom_usuari (sense @domini) |

---

## 3. Dashboard SIEM (Wazuh)

**Accés:** `https://ip-del-servidor-wazuh` (acceptar certificat autofirmat al primer accés)

### Seccions principals

| Secció | Descripció |
|---|---|
| Security Events | Alertes de seguretat en temps real |
| Integrity Monitoring | Canvis en fitxers crítics |
| Vulnerability Detector | Vulnerabilitats detectades |
| MITRE ATT&CK | Mapa de tàctiques i tècniques d'atac |

### Llegir una alerta

Les alertes es classifiquen per nivell:

| Nivell | Significat |
|---|---|
| 1-4 | Informatiu |
| 5-7 | Alerta baixa |
| 8-10 | Alerta alta |
| 12-15 | Crítica |

### Filtrar alertes

Al quadre de cerca del Dashboard es poden aplicar filtres com:

```
rule.level: 10+
agent.name: nom_agent
data.srcip: xarxa_origen
```

### Investigació d'incidents

1. Detectar l'alerta a **Security Events** (nivell > 10)
2. Filtrar per IP origen sospitosa
3. Revisar l'agent afectat a l'apartat **Agents**
4. Correlacionar amb **MITRE ATT&CK** per identificar la tàctica d'atac
5. Documentar l'incident

---

## 4. FTP

### Connexió

```bash
ftp ip-del-servidor
```

Autenticar-se amb usuari i contrasenya del sistema.

### Pujar un fitxer

```bash
ftp> put fitxer.txt
```

---

## 5. Monitorització (Grafana)

**Accés:** `http://ip-del-servidor:3000`

### Dashboards disponibles

| Dashboard | Què mostra |
|---|---|
| Node Exporter Full | CPU, RAM, disc, processos del servidor |
| Blackbox Exporter | Estat dels serveis web |
| LAN Completa | Estat de tots els hosts de la xarxa |
| Prometheus Overview | Estat intern de Prometheus |

### Interpretació

| Color | Significat |
|---|---|
| Verd | Normal |
| Taronja | Advertència |
| Vermell | Crític / Error |

---

## 6. Base de dades (phpMyAdmin)

**Accés:** `http://ip-del-servidor-bd/phpMyAdmin`

Iniciar sessió amb l'usuari assignat. Segons el rol de l'usuari:
- **Administrador**: accés complet (crear, modificar, eliminar)
- **Analista**: consultar, inserir i actualitzar dades
- **Lector**: només consultar (SELECT), sense modificar res

---

## 7. Honeypot (Cowrie)

El dashboard web del honeypot mostra tots els intents de connexió, autenticacions fallides i comandes executades pels atacants. Serveix per analitzar el comportament d'atacants potencials.

---

## 8. Correu electrònic (IMAP/SMTP)

Els usuaris poden accedir al correu mitjançant:
- Roundcube (webmail): via navegador web
- Client de correu extern: amb IMAP (port 143) i SMTP (port 25)

