# 🚀 Monitoramento Profissional na AWS
## Observabilidade End-to-End com EC2, CloudWatch e Neo4j

> Um projeto completo demonstrando habilidades em **Cloud Computing**, **DevOps** e **Observabilidade** usando a stack moderna da AWS integrada com visualização de grafos em tempo real.

---

## 📋 Índice

- [Visão Geral](#-visão-geral)
- [Arquitetura](#-arquitetura)
- [Requisitos](#-requisitos)
- [Instalação e Configuração](#-instalação-e-configuração)
- [Componentes Principais](#-componentes-principais)
- [Uso](#-uso)
- [Roadmap](#-roadmap)
- [Autor](#-autor)

---

## 🎯 Visão Geral

Este projeto implementa uma solução **profissional e escalável** de monitoramento e observabilidade, demonstrando um fluxo real de **Suporte Cloud/DevOps**. Combina as melhores práticas da AWS com visualização avançada de dados em grafos.

### ✨ Principais Características

- **Coleta automática de métricas**: CPU, Memória, Disco
- **Logs centralizados**: Agregação de logs do Apache via CloudWatch
- **Alertas inteligentes**: Disparados via SNS com notificações por e-mail
- **Dashboard customizado**: Visualização em tempo real das métricas
- **Visualização em Grafos**: Neo4j para análise de eventos e relacionamentos
- **Infraestrutura segura**: Security Groups e isolamento de rede configurados

---

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────────────────────────────┐
│                         EC2 Instance                             │
│                      (Ubuntu 24.04 LTS)                          │
│                                                                  │
│  ┌──────────────────┐         ┌──────────────────────────┐      │
│  │  Apache Server   │◄────────┤  CloudWatch Agent        │      │
│  │   (porta 80)     │         │  • CPU Metrics           │      │
│  └──────────────────┘         │  • Memory Usage          │      │
│         │                     │  • Disk Space            │      │
│    logs/errors                │  • Log Collection        │      │
│         │                     └──────────────┬───────────┘      │
│         └────────────────────────────────────┘                  │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
                    ┌────────────────────────┐
                    │   AWS CloudWatch       │
                    │  Metrics | Logs | Alarms
                    └────────┬───────────────┘
                             │
                ┌────────────┼────────────┐
                ▼            ▼            ▼
         ┌──────────┐  ┌──────────┐  ┌─────────┐
         │ Dashboard│  │  Alarmes │  │   SNS   │
         │ Métricas │  │  (CPU%)  │  │  Email  │
         └──────────┘  └──────────┘  └─────────┘
                             │
         ┌───────────────────┘
         ▼
    ┌─────────────────┐
    │   Neo4j (7474)  │
    │  Grafo Events   │
    │  Visualização   │
    └─────────────────┘
```

---

## 📦 Requisitos

### Hardware
- **EC2 Instance**: `t3.micro` ou superior
- **Storage**: 20GB mínimo
- **Network**: Security Group com portas abertas (80, 443, 7474, 7687)

### Software
- Ubuntu 24.04 LTS
- Apache 2.4+
- CloudWatch Agent
- Neo4j 5.x
- AWS CLI configurado

### Credenciais
- **AWS Account** com permissões IAM para:
  - EC2
  - CloudWatch (Logs, Metrics, Alarms)
  - SNS

---

## 🔧 Instalação e Configuração

### 1️⃣ Inicializar EC2

```bash
# Atualizar repositórios
sudo apt update && sudo apt upgrade -y

# Instalar Apache
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl start apache2

# Customizar página inicial
echo '<h1>🚀 Servidor de Monitoramento AWS</h1>' | sudo tee /var/www/html/index.html
echo '<p>Jailson - Cloud & DevOps</p>' | sudo tee -a /var/www/html/index.html
```

### 2️⃣ Instalar CloudWatch Agent

```bash
# Download do agent
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb

# Instalação
sudo dpkg -i amazon-cloudwatch-agent.deb

# Criar arquivo de configuração
sudo nano /opt/aws/amazon-cloudwatch-agent/bin/config.json
```

**Exemplo de configuração (config.json)**:

```json
{
  "metrics": {
    "namespace": "CustomMonitoring",
    "metrics_collected": {
      "cpu": {
        "measurement": [
          {
            "name": "cpu_usage_idle",
            "rename": "CPU_IDLE",
            "unit": "Percent"
          },
          "cpu_usage_iowait"
        ],
        "totalcpu": false,
        "metrics_collection_interval": 60
      },
      "mem": {
        "measurement": [
          {
            "name": "mem_used_percent",
            "rename": "MEMORY_USED",
            "unit": "Percent"
          }
        ],
        "metrics_collection_interval": 60
      },
      "disk": {
        "measurement": [
          {
            "name": "used_percent",
            "rename": "DISK_USED",
            "unit": "Percent"
          }
        ],
        "resources": ["/"],
        "metrics_collection_interval": 60
      }
    }
  },
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/apache2/error.log",
            "log_group_name": "/aws/ec2/apache-errors",
            "log_stream_name": "{instance_id}"
          },
          {
            "file_path": "/var/log/apache2/access.log",
            "log_group_name": "/aws/ec2/apache-access",
            "log_stream_name": "{instance_id}"
          }
        ]
      }
    }
  }
}
```

### 3️⃣ Ativar CloudWatch Agent

```bash
# Executar agent com configuração
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
  -s

# Verificar status
systemctl status amazon-cloudwatch-agent
```

### 4️⃣ Instalar Neo4j

```bash
# Adicionar repositório
wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -
echo "deb https://debian.neo4j.com stable 5" | sudo tee /etc/apt/sources.list.d/neo4j.list

# Instalar
sudo apt update
sudo apt install neo4j -y

# Habilitar serviço
sudo systemctl enable neo4j
sudo systemctl start neo4j

# Verificar status
sudo systemctl status neo4j
```

### 5️⃣ Configurar Neo4j para Acesso Externo

```bash
# Editar configuração
sudo nano /etc/neo4j/neo4j.conf
```

Adicionar/modificar as linhas:

```conf
server.default_listen_address=0.0.0.0
server.http.listen_address=:7474
server.bolt.listen_address=:7687
```

Reiniciar:

```bash
sudo systemctl restart neo4j
```

### 6️⃣ Configurar Security Group

Adicionar regras de entrada:

| Protocolo | Porta | Origem    | Descrição      |
|-----------|-------|-----------|----------------|
| TCP       | 80    | 0.0.0.0/0 | HTTP Apache    |
| TCP       | 443   | 0.0.0.0/0 | HTTPS          |
| TCP       | 7474  | 0.0.0.0/0 | Neo4j Browser  |
| TCP       | 7687  | 0.0.0.0/0 | Neo4j Bolt     |

---

## 📸 Evidências do Projeto

### Dashboard CloudWatch

![CloudWatch Dashboard 1](https://github.com/user-attachments/assets/55face8a-0eb3-47bd-a751-ca4a5a77363c)
*Dashboard principal com métricas de CPU, Memória e Disco*

![CloudWatch Dashboard 2](https://github.com/user-attachments/assets/c7231153-3d5f-4cb6-ba64-2fc77b127f22)
*Visualização detalhada de logs e filtros de erros*

### Neo4j e Monitoramento

![Neo4j Graph Visualization](https://github.com/user-attachments/assets/d4fbca5d-82b4-46ce-9657-77bfb224eb96)
*Grafo de eventos mostrando relacionamentos entre IPs, Logs e Status*

![System Monitoring](https://github.com/user-attachments/assets/ba0accfa-a634-4f09-860a-61f32182913b)
*Interface de monitoramento em tempo real*

---

## 🔍 Componentes Principais

### CloudWatch Dashboard

Acesse o dashboard customizado com:

- **Gráficos de CPU**: Visualização de picos e médias
- **Memória**: Tendências de consumo
- **Disco**: Alertas de espaço disponível
- **Logs filtrados**: Apenas erros do Apache

**Query CloudWatch Logs**:

```
fields @timestamp, @message
| filter @message like /ERROR/
| stats count() by @message
| sort @timestamp desc
```

### Alarmes Configurados

#### ⚠️ Alarme 1: CPU > 70%

- **Métrica**: CPUUsage
- **Threshold**: 70%
- **Período**: 5 minutos
- **Ação**: SNS → E-mail

#### ⚠️ Alarme 2: Disco > 80%

- **Métrica**: disk_used_percent
- **Threshold**: 80%
- **Período**: 5 minutos

#### ⚠️ Alarme 3: Erros do Apache

- **Tipo**: Metric Filter
- **Pattern**: `ERROR`
- **Ação**: Notificação imediata

### Neo4j - Modelagem de Grafos

Estrutura de dados para rastreamento de eventos:

```cypher
CREATE (l:LogEntry {
  message: "ERROR File not found",
  route: "/naoexiste",
  status: 404,
  timestamp: timestamp()
});

CREATE (ip:IP { address: "192.168.1.100" });
CREATE (s:Status { code: 404, description: "Not Found" });
CREATE (r:Route { path: "/naoexiste", method: "GET" });

CREATE (ip)-[:GEROU]->(l);
CREATE (l)-[:RETORNOU]->(s);
CREATE (l)-[:OCORREU_EM]->(r);
```

**Visualização em Grafo**:

```
[IP: 192.168.1.100] --GEROU--> [LogEntry: ERROR] --RETORNOU--> [Status: 404]
                                      |
                                      v
                              [Route: /naoexiste]
```

---

## 📊 Uso

### Acessar a Aplicação

| Serviço       | URL                              | Descrição        |
|---------------|---------------------------------|------------------|
| Apache        | `http://<EC2-IP>`               | Página principal |
| Neo4j Browser | `http://<EC2-IP>:7474`          | Visualização     |
| CloudWatch    | AWS Console → CloudWatch        | Métricas/Logs    |

### Consultar Métricas

```bash
# Via AWS CLI
aws cloudwatch get-metric-statistics \
  --namespace CustomMonitoring \
  --metric-name CPU_IDLE \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-02T00:00:00Z \
  --period 300 \
  --statistics Average
```

### Gerar Carga (Teste)

```bash
# Simular erro HTTP
curl http://localhost/naoexiste

# Gerar carga de CPU
stress-ng --cpu 4 --timeout 60s
```

---

## 🚀 Roadmap

### ✅ Concluído
- [x] EC2 com Apache
- [x] CloudWatch Agent com métricas
- [x] CloudWatch Dashboard
- [x] Alarmes integrados
- [x] SNS com notificações
- [x] Neo4j instalado e configurado
- [x] Modelagem manual de grafos

### 🔄 Em Desenvolvimento
- [ ] Script Python automático (CloudWatch → Neo4j)
- [ ] Lambda function para integração
- [ ] Grafos em tempo real
- [ ] Serviço systemd para monitoramento contínuo
- [ ] Dashboard Neo4j customizado
- [ ] Análise de anomalias com Machine Learning

### 📋 Planejado
- [ ] Terraform para IaC
- [ ] GitHub Actions CI/CD
- [ ] Documentação de API
- [ ] Testes automatizados

---

## 🛠️ Troubleshooting

### CloudWatch Agent não coleta métricas

```bash
# Verificar logs
sudo tail -f /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log

# Reiniciar
sudo systemctl restart amazon-cloudwatch-agent
```

### Neo4j inacessível externamente

```bash
# Verificar se está rodando
sudo systemctl status neo4j

# Verificar portas abertas
sudo netstat -tlnp | grep 7474

# Verificar Security Group no Console AWS
```

### Logs não aparecem no CloudWatch

```bash
# Verificar permissões da role EC2
# Assegurar que a role tem: CloudWatchAgentServerPolicy

# Forçar reinicialização do agent
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
  -s
```

---

## 📁 Estrutura do Projeto

```
.
├── README.md                    # Este arquivo
├── config.json                  # Configuração CloudWatch Agent
├── scripts/
│   ├── install.sh              # Script de instalação automatizada
│   └── neo4j-sync.py           # (Futuro) Sincronização com Neo4j
├── terraform/                  # (Futuro) Infraestrutura como código
└── docs/
    ├── SETUP.md               # Guia detalhado de setup
    └── QUERIES.md             # Queries úteis do Neo4j
```

---

## 🎓 Habilidades Demonstradas

✅ **Cloud Infrastructure**
- EC2 provisioning e gerenciamento
- Security Groups e isolamento de rede
- IAM roles e permissões

✅ **Observabilidade**
- CloudWatch Metrics e Logs
- Dashboards customizados
- Alertas e notificações

✅ **DevOps**
- Linux/Ubuntu administration
- Apache HTTP Server
- Agent deployment e configuração

✅ **Banco de Dados em Grafos**
- Neo4j setup e configuração
- Modelagem de relacionamentos
- Cypher queries

✅ **Networking**
- TCP/IP e DNS
- Portas e protocolos
- Segurança de rede

---

## 📧 Contato & Suporte

**Autor**: Jailson  
**Email**: serviço@portifolejailson.com.br  
**LinkedIn**: [jailsonanalista](https://linkedin.com/in/jailsonanalista)  
**GitHub**: [seu-usuario](https://github.com)

---

## 📄 Licença

Este projeto é **MIT Licensed** - veja a licença completa em `LICENSE.md`

---

## 🙏 Agradecimentos

- AWS Documentation
- Neo4j Community
- Apache Software Foundation
- Stack Overflow Community

---

**Última atualização**: Novembro 2025  
**Status**: ✅ Produção
