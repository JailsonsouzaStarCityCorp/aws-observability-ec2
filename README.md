# Monitoramento Completo na AWS com EC2, Apache, CloudWatch Agent, Logs, Métricas, Alarmes e Dashboard

Este projeto implementa uma arquitetura completa de **monitoramento em nuvem**, utilizando Amazon EC2, Apache, CloudWatch Agent, métricas personalizadas, logs centralizados, alarmes inteligentes e um dashboard profissional. O objetivo é demonstrar habilidades práticas de **Cloud Support, Observabilidade e DevOps Jr.**, sendo ideal para portfólio, GitHub e entrevistas técnicas.

---

## 🏗️ Arquitetura do Projeto

**Componentes principais:**

* **Amazon EC2:** Servidor Linux rodando Apache
* **Apache2:** Serviço monitorado via logs e métricas
* **CloudWatch Agent:** Captura CPU, RAM, Disco e envia para o CloudWatch
* **CloudWatch Logs:** Armazena erros do Apache em log groups
* **Metric Filter:** Cria métricas baseadas em padrões de log (ex.: "ERROR")
* **SNS:** Notificações por e-mail
* **CloudWatch Alarms:** Disparam eventos com CPU alta, Disco cheio e Erros do Apache
* **Dashboard:** Painel visual consolidado

**Fluxo:**

```
EC2 → CloudWatch Agent → CloudWatch Metrics → CloudWatch Alarms → SNS → E-mail
                                 ↘
                                   CloudWatch Logs → Metric Filter → ApacheErrors
```

---

## ⚙️ Configuração da EC2 + Apache

Instalar Apache:

```
sudo apt update
sudo apt install apache2 -y
```

Criar página simples:

```
echo "Servidor de Monitoramento AWS - Jailson" | sudo tee /var/www/html/index.html
```

Verificar serviço:

```
systemctl status apache2
```

---

## 📦 Instalação do CloudWatch Agent

Download e instalação:

```
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb
```

Configuração:

```
sudo nano /opt/aws/amazon-cloudwatch-agent/bin/config.json
```

Configuração utilizada (resumo):

* CPU (user/system/idle)
* RAM
* Disco (/)
* Logs do Apache error.log

Aplicar a configuração:

```
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
```

Verificar status:

```
systemctl status amazon-cloudwatch-agent
```

---

## 🔐 IAM Role para permitir envio de métricas

Criada função **EC2-CloudWatchAgent-Role** com política:

```
CloudWatchAgentServerPolicy
```

Associada à instância EC2 via:

```
EC2 → Actions → Security → Modify IAM Role
```

---

## 📝 Logs do Apache no CloudWatch

O agente envia automaticamente o arquivo:

```
/var/log/apache2/error.log
```

para o log group:

```
apache_error_log
```

---

## 📊 Criação das Métricas Personalizadas (Metric Filter)

Filtro criado para identificar erros do Apache:

```
Filter pattern: ERROR
```

Métrica gerada:

```
Namespace: CWAgent/Apache
Metric Name: ApacheErrors
Value: 1
```

---

## 🚨 Alarmes Criados

### 1️⃣ Alarme de CPU Alta

```
Métrica: cpu_usage_user
Condição: > 70%
Período: 1 minuto
Ação: SNS → alertas_support
```

### 2️⃣ Alarme de Disco Cheio

```
Métrica: used_percent
Condição: > 80%
Período: 1 minuto
```

### 3️⃣ Alarme de Erros do Apache

```
Métrica: ApacheErrors
Condição: > 0
Período: 1 minuto
```

---

## 📈 Dashboard Profissional

Widgets adicionados:

* Gráfico de CPU (user/system/idle)
* RAM (mem_used_percent)
* Disco (used_percent)
* Logs do Apache filtrando ERROR
* Estado dos alarmes (CPU, Disco, Apache)

Dashboard: **Monitoramento-AWS-Jailson**

---

## 🧪 Testes Realizados

### Teste de CPU alta:

```
sudo apt install stress -y
stress --cpu 4 --timeout 120
```

### Teste de Disco cheio:

```
sudo fallocate -l 3G /bigfile
sudo rm /bigfile
```

### Teste de erro no Apache:

```
sudo systemctl stop apache2
```

Acessar o IP para gerar erro.

---

## 💻 Como Reproduzir o Projeto

1. Criar EC2 Linux
2. Instalar Apache
3. Instalar CloudWatch Agent
4. Configurar JSON de métricas e logs
5. Criar IAM Role e anexar à EC2
6. Validar métricas no CloudWatch
7. Criar filtros e alarmes
8. Montar dashboard

---

## 📚 Tecnologias Utilizadas

* AWS EC2
* Apache2
* CloudWatch Logs
* CloudWatch Metrics
* CloudWatch Agent
* SNS
* IAM

---

## 🏁 Conclusão

Este projeto demonstra domínio completo de:

* Observabilidade
* Monitoramento em tempo real
* Diagnóstico de serviços
* Detecção de incidentes
* Estruturação de métricas e logs
* Criação de dashboards profissionais
* Engenharia de suporte e operação em nuvem

Uma solução pronta para equipes de **Cloud, SRE, NOC e DevOps Jr.**.

---

## 📸 Prints do Projeto

<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/55face8a-0eb3-47bd-a751-ca4a5a77363c" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/c7231153-3d5f-4cb6-ba64-2fc77b127f22" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/d4fbca5d-82b4-46ce-9657-77bfb224eb96" />






