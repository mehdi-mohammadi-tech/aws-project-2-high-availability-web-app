# AWS Project 2: Hochverfügbare Web-Anwendung

## 📋 Projekt-Beschreibung

Dieses Projekt demonstriert eine **hochverfügbare Web-Anwendung** auf AWS mit automatischer Skalierung über zwei Availability Zones. Es verbindet mehrere AWS-Services zu einer robusten Infrastruktur, die automatisch auf Last reagiert.

**Live-Demo:** ALB DNS: `projekt2-alb-341637141.eu-central-1.elb.amazonaws.com`

---

## 🏗️ Architektur

```
┌─────────────────────────────────────────────────────────────┐
│                        Internet (Besucher)                   │
└────────────────────────────┬────────────────────────────────┘
                             │
                             ▼
        ┌────────────────────────────────────────┐
        │  Application Load Balancer (ALB)       │
        │  Port 80 (HTTP)                        │
        │  DNS: projekt2-alb-...elb.amazonaws.com│
        └────────────┬───────────────────────────┘
                     │
        ┌────────────┴────────────────┐
        │                             │
        ▼                             ▼
    ┌────────────┐             ┌────────────┐
    │ AZ-A (1a)  │             │ AZ-B (1b)  │
    ├────────────┤             ├────────────┤
    │ Server 1   │             │ Server 2   │
    │ t2.micro   │             │ t2.micro   │
    │ Apache     │             │ Apache     │
    │ Port 80    │             │ Port 80    │
    └────────────┘             └────────────┘
        │                             │
        └────────────┬────────────────┘
                     │
        ┌────────────▼────────────────┐
        │  Auto Scaling Group (ASG)   │
        │  Min: 2, Max: 4             │
        │  Auto-Scale je nach Last    │
        └─────────────────────────────┘
                     │
        ┌────────────▼────────────────┐
        │  VPC: 10.0.0.0/16           │
        │  - 2 Public Subnets         │
        │  - 2 Private Subnets        │
        │  - Internet Gateway         │
        └─────────────────────────────┘
```

---

## 🛠️ AWS Services verwendet

| Service | Zweck | Kosten |
|---------|-------|--------|
| **VPC** | Privates Netzwerk | Kostenlos |
| **EC2** | Web-Server (t2.micro) | Free Tier |
| **ALB** | Load Balancing | ~$16/Monat |
| **Auto Scaling** | Automatische Skalierung | Kostenlos |
| **Security Groups** | Firewall | Kostenlos |
| **Launch Template** | Server-Vorlage | Kostenlos |
| **Target Group** | ALB-Ziele | Kostenlos |

**Geschätzte monatliche Kosten:** ~$0-5 (mit Free Tier)

---

## 📚 Projekt-Schritte (4 Parts)

### Part 1: VPC & Netzwerk-Infrastruktur ✅
- VPC erstellt: `10.0.0.0/16`
- 2 Availability Zones (AZ-A + AZ-B)
- 4 Subnets: 2 public + 2 private
- Internet Gateway
- DNS aktiviert

**Services:** VPC, Subnets, Internet Gateway

### Part 2: EC2 & Security ✅
- 2 EC2 Server (t2.micro)
- Amazon Linux 2
- Apache Web-Server installiert
- SSH-Zugriff konfiguriert
- Security Groups mit HTTP (80) + SSH (22)
- AMI-Image erstellt für Replikation

**Services:** EC2, Security Groups, AMI, Key Pairs

### Part 3: Application Load Balancer ✅
- ALB erstellt: `projekt2-alb`
- Listener: HTTP Port 80
- Target Group: `projekt2-tg`
- Beide Server registriert (AZ-A + AZ-B)
- Health Checks konfiguriert

**Services:** ALB, Target Groups, Health Checks

### Part 4: Auto Scaling ✅
- Launch Template erstellt
- Auto Scaling Group konfiguriert
  - Min: 2 Server
  - Max: 4 Server
- Mit ALB verbunden
- Automatische Skalierung basierend auf Last

**Services:** Auto Scaling, Launch Templates

---

## 🔑 Wichtigste Konzepte gelernt

### 1. **High Availability (Hochverfügbarkeit)**
- ✅ 2 Server in verschiedenen Availability Zones
- ✅ Wenn AZ-A ausfällt → AZ-B läuft weiter
- ✅ 99.99% Verfügbarkeit möglich

### 2. **Load Balancing**
- ✅ ALB verteilt Traffic intelligently
- ✅ Health Checks prüfen Server-Status
- ✅ Automatisches Failover

### 3. **Auto Scaling**
- ✅ Automatisch neue Server bei hoher Last
- ✅ Automatisch Server bei niedriger Last stoppen
- ✅ Spart Kosten durch Bedarfsanpassung

### 4. **Infrastructure as Code Mindset**
- ✅ Launch Templates für Automatisierung
- ✅ Wiederverwendbare Konfigurationen
- ✅ Schnelle Skalierung möglich

### 5. **Security Best Practices**
- ✅ Security Groups mit Least Privilege
- ✅ SSH nur mit Key Pair
- ✅ HTTP nur vom ALB zu Servern

---

## 📊 Architektur-Vorteile

| Vorteil | Beschreibung |
|---------|-------------|
| **Hochverfügbarkeit** | 2+ Server in verschiedenen AZs |
| **Auto Scaling** | Automatische Anpassung an Last |
| **Kosteneffizienz** | Skaliert mit Bedarf (pay-as-you-go) |
| **Fehlertoleranz** | Server-Ausfälle beeinflussen nicht Service |
| **Globale Reichweite** | ALB verteilt zu AWS Regionen |

---

## 💡 Lessons Learned

### ✅ Was gut war
1. **VPC-Struktur:** Saubere Aufteilung in Public/Private Subnets
2. **Security:** Strikte Security Groups für einzelne Services
3. **Automation:** Auto Scaling reduziert manuelle Arbeit
4. **Redundanz:** 2 AZs garantiert verfügbar

### ⚠️ Verbesserungsmöglichkeiten
1. **HTTPS:** Sollte mit SSL/TLS verschlüsselt sein
2. **Database:** Noch keine Persistierung (Project 3)
3. **Monitoring:** CloudWatch Alerts für besseres Monitoring
4. **Cost Optimization:** Reserved Instances könnten Kosten sparen

---

## 🔄 Replikation (Wie man das baut)

### Schnelle Anleitung:
1. VPC mit 2 AZs erstellen
2. EC2 Server (t2.micro) in beiden AZs
3. Apache installieren + index.html
4. AMI von Server 1 erstellen
5. Server 2 von AMI starten
6. ALB mit Target Group erstellen
7. Auto Scaling Group mit Launch Template
8. Auf ALB DNS testen

### Kosten-Tipps:
- ✅ Free Tier: 750h t2.micro/Monat
- ✅ ALB: ~$16/Monat (minimal für Prod)
- ❌ NAT Gateway: $32/Monat (vermieden)
- ✅ Stoppe Server wenn nicht gebraucht!

---

## 📈 Monitoring

### Health Checks
```
ALB → Health Check Port 80 (HTTP /)
→ Server antwortet ✅ → Healthy
→ Server antwortet ❌ → Unhealthy
→ Auto Scaling ersetzt Server
```

### Auto Scaling Policies
- **Scale Up:** CPU > 70% → Neue Server starten
- **Scale Down:** CPU < 30% → Überzählige Server stoppen

---

## 🚀 Nächste Schritte (Project 3)

Dieses Projekt bildet die Basis für:

1. **RDS Database Layer** (Project 3)
   - Verbindung dieser Server zu RDS
   - Dynamische Datenbanken

2. **Security Hardening**
   - HTTPS/SSL mit ACM
   - WAF (Web Application Firewall)

3. **Cost Optimization**
   - Reserved Instances
   - Spot Instances

4. **Advanced Scaling**
   - Target Tracking Policies
   - Scheduled Scaling

---

## 📸 Screenshots

### ALB Details
```
Load Balancer: projekt2-alb
DNS: projekt2-alb-341637141.eu-central-1.elb.amazonaws.com
Status: Active
Availability Zones: eu-central-1a, eu-central-1b
```

### Auto Scaling Group Status
```
Name: projekt2-asg
Desired Capacity: 2
Min: 2, Max: 4
Running Instances: 2
Status: Healthy
```

### Website Response
```
Hallo! Das ist mein Server!
Apache läuft auf EC2. IP: 10.0.x.x
(Load Balancer verteilt zu verschiedenen IPs)
```

---

## 🎓 AWS Services Beherrscht

Nach diesem Projekt kannst du:

✅ VPC-Netzwerke designen
✅ EC2-Instanzen provisioned
✅ Security Groups konfigurieren
✅ Load Balancer deployen
✅ Auto Scaling einrichten
✅ Launch Templates erstellen
✅ High Availability architektieren

---

## 📝 Repository-Info

- **Repository:** aws-project-2-high-availability-web-app
- **Autor:** Mehdi Mohammadi
- **Zertifikation:** SAA-C03 (AWS Solutions Architect Associate)
- **Start Datum:** Mai 30, 2026
- **Portfolio:** https://github.com/mehdi-mohammadi-tech

---

## 🔗 Zusätzliche Ressourcen

- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [ALB Documentation](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/)
- [Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/)

---

**Status:** ✅ Komplett | **Dauer:** 6 Stunden | **Kosten:** ~$0-5/Monat
