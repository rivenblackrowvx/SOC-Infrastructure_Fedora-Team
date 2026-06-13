# Manual d'Administració

> **Què s'explica en aquest document?** Tasques d'administració i manteniment de cada servei: gestió d'usuaris, logs, backups, actualitzacions, resolució d'incidències i troubleshooting bàsic. Orientat a administradors de sistemes.

---

## 1. Apache

### Fitxers importants

| Fitxer | Descripció |
|---|---|
| `/etc/httpd/conf/httpd.conf` | Configuració global |
| `/etc/httpd/conf.d/` | VirtualHosts |
| `/var/www/` | Arrel web |
| `/etc/httpd/ssl/` | Certificats SSL |

### Gestió del servei

```bash
sudo systemctl start httpd
sudo systemctl stop httpd
sudo systemctl restart httpd
sudo systemctl status httpd
```

Renovar certificat SSL:

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/httpd/ssl/domini.key \
  -out /etc/httpd/ssl/domini.crt
```

---

## 2. OPNsense

### DHCP
**Services → Kea DHCP → Kea DHCPv4 → Subnets**

### DNS
**Services → Unbound DNS** per gestionar els registres DNS locals.

### Suricata (IDS/IPS)
**Services → Intrusion Detection → Administration** per gestionar alertes, descarregar i habilitar/deshabilitar regles.

---

## 3. Roundcube

### Crear un usuari de correu

```bash
sudo useradd -m -s /sbin/nologin nom_usuari
sudo passwd nom_usuari
```

### Logs

```bash
sudo journalctl -u postfix -n 50 --no-pager
sudo journalctl -u dovecot -n 50 --no-pager
```

### Backup

```bash
sudo mysqldump -u root -p roundcubemail > /backup/roundcubemail_$(date +%Y%m%d).sql
```

---

## 4. Wazuh + Suricata (SIEM)

### Gestió d'agents

```bash
# Llistar agents
sudo /var/ossec/bin/agent_control -l

# Registrar agent manualment
sudo /var/ossec/bin/manage_agents

# Forçar reconnexió
sudo /var/ossec/bin/agent_control -r -u 001
```

### Gestió de serveis

```bash
sudo systemctl restart wazuh-manager
sudo systemctl restart wazuh-indexer
sudo systemctl restart wazuh-dashboard
sudo systemctl restart suricata
```

### Logs en temps real

```bash
journalctl -u wazuh-manager -f
journalctl -u suricata -f
```

### Regles personalitzades

Fitxer: `/var/ossec/etc/rules/local_rules.xml`

```bash
sudo systemctl restart wazuh-manager
```

### Mòduls de Wazuh

| Mòdul | Funció |
|---|---|
| Security Events | Alertes en temps real |
| Integrity Monitoring | Canvis en fitxers crítics |
| Vulnerability Detector | CVE detectats |
| MITRE ATT&CK | Correlació amb tàctiques d'atac |

### Backups

```bash
sudo tar -czf /opt/backups/ossec-$(date +%F).tar.gz /var/ossec/etc/
sudo tar -czf /opt/backups/suricata-$(date +%F).tar.gz /etc/suricata/
```

### Actualitzacions

```bash
sudo apt update && sudo apt upgrade wazuh-manager wazuh-indexer wazuh-dashboard -y
sudo suricata-update && sudo systemctl reload suricata
```

---

## 5. vsftpd (FTP)

### Gestió del servei

```bash
sudo systemctl status vsftpd
sudo systemctl restart vsftpd
```

### Gestió d'usuaris

```bash
sudo useradd usuariftp
sudo passwd usuariftp
sudo userdel usuariftp
```

### Logs

```bash
sudo journalctl -u vsftpd -f
```

---

## 6. Grafana + Prometheus

### Serveis

| Servei | Comanda |
|---|---|
| Prometheus | `sudo systemctl restart prometheus` |
| Grafana | `sudo systemctl restart grafana-server` |
| Node Exporter | `sudo systemctl restart prometheus-node-exporter` |
| Blackbox Exporter | `sudo systemctl restart prometheus-blackbox-exporter` |

### Logs

```bash
sudo journalctl -u prometheus -n 50 --no-pager
sudo journalctl -u grafana-server -n 50 --no-pager
```

### Afegir un host a la monitorització

1. Escanejar el nou host amb Nmap
2. Afegir la IP al fitxer `/etc/prometheus/prometheus.yml`
3. Recarregar Prometheus: `sudo systemctl reload prometheus`

---

## 7. MariaDB

### Gestió del servei

```bash
sudo systemctl start mariadb
sudo systemctl stop mariadb
sudo systemctl restart mariadb
sudo systemctl status mariadb
```

### Backup

```bash
mysqldump -u root -p grup5 > grup5_backup.sql
mysql -u root -p grup5 < grup5_backup.sql
```

### Logs

```bash
sudo journalctl -u mariadb -f
```

---

## 8. Postfix + Dovecot

### Gestió dels serveis

```bash
sudo systemctl restart postfix
sudo systemctl restart dovecot
sudo systemctl status postfix
sudo systemctl status dovecot
```

### Validació

```bash
sudo postfix check
sudo doveconf
```
