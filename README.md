Projeto de Monitoramento AWS + Apache + CloudWatch + Neo4j
📌 Visão Geral

Este projeto implementa um ambiente completo de monitoramento e observabilidade utilizando:

AWS EC2

Apache HTTP Server

AWS CloudWatch Agent

Métricas personalizadas

Logs centralizados

Alarmes

SNS (alertas por e-mail)

Neo4j para visualização de eventos em grafo

O objetivo é demonstrar um fluxo real de suporte/Cloud/DevOps, elevando o projeto ao nível profissional.

🟦 Arquitetura do Projeto
┌──────────┐       ┌────────────────────┐
│  Usuário │──────▶│ Apache (porta 80)  │
└──────────┘       └────────────────────┘
                       │ Logs / Erros
                       ▼
             ┌────────────────────┐
             │ CloudWatch Agent   │
             │  (Métricas + Logs) │
             └────────────────────┘
              │ CPU │ RAM │ Disco │
              ▼
     ┌────────────────┐
     │ CloudWatch     │
     │  Logs & Metrics│
     └────────────────┘
      │ Alarmes (SNS)
      ▼
┌────────────────────┐
│ E-mail (alertas)   │
└────────────────────┘

 ┌────────────────────┐
 │ Neo4j (porta 7474) │
 │ Visualização em    │
 │ Grafos de eventos  │
 └────────────────────┘

🟦 1. Configuração da EC2

Ubuntu 24.04 LTS

Instalação do Apache:

sudo apt update
sudo apt install apache2 -y


Página customizada:

echo "Servidor de Monitoramento AWS - Jailson" | sudo tee /var/www/html/index.html

🟦 2. CloudWatch Agent
✔ Instalação
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb

✔ Configuração criada por você (config.json)

Coleta:

CPU

Memória

Disco

Log de Erros do Apache (/var/log/apache2/error.log)

Ativação:

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
  -a fetch-config \
  -m ec2 \
  -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
  -s

✔ Verificação
systemctl status amazon-cloudwatch-agent

🟦 3. CloudWatch Dashboard

Você criou um dashboard profissional com:

✔ Gráficos de:

CPUUsage

MemoryUsage

DiskUsage

✔ Logs filtrados por:
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 20

✔ Widgets de métricas + métricas customizadas
✔ Screenshots do painel podem ser adicionados aqui
🟦 4. Alarmes Configurados
✔ Alarme 1 — CPU > 70%

Threshold: 70%

Estatística: Average

Período: 5 minutos

Ação: Envio para SNS

✔ Alarme 2 — Disco > 80%

Métrica usada: disk_used_percent

Estatística: Maximum

Período: 5 minutos

✔ Alarme 3 — Erros do Apache

Criado com Metric Filter usando:

ERROR


Estatística: Sum
Alarme dispara ao detectar qualquer erro.

🟦 5. SNS — Notificação por E-mail

Você configurou:

Tópico SNS

Assinatura via e-mail

Confirmação do e-mail

Alarmes integrados ao SNS

🟦 6. Neo4j (Integração Manual Inicial)
✔ Instalação
wget -O - https://debian.neo4j.com/neotechnology.gpg.key | sudo apt-key add -
echo "deb https://debian.neo4j.com stable 5" | sudo tee /etc/apt/sources.list.d/neo4j.list

sudo apt update
sudo apt install neo4j -y

✔ Liberação de portas no Security Group

7474 (HTTP)

7687 (Bolt)

✔ Configuração para acesso externo

Arquivo: /etc/neo4j/neo4j.conf

server.default_listen_address=0.0.0.0
server.http.listen_address=:7474
server.bolt.listen_address=:7687

✔ Testes realizados

Criados manualmente no Neo4j:

Label LogEntry

Label IP

Label Route

Label Status

Exemplo criado:

CREATE (l:LogEntry {
 message:"ERROR File not found",
 route:"/naoexiste",
 status:404,
 timestamp:timestamp()
});


Relacionamentos:

(ip)-[:GEROU]->(l)
(l)-[:RETORNOU]->(s)
(l)-[:OCORREU_EM]->(r)


Isso cria um grafo profissional representando:

(IP) → GEROU → (LogEntry) → RETORNOU → (Status)
                            ↘
                             OCORREU_EM → (Route)

🟦 7. Prints e Evidências

<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/55face8a-0eb3-47bd-a751-ca4a5a77363c" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/c7231153-3d5f-4cb6-ba64-2fc77b127f22" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/d4fbca5d-82b4-46ce-9657-77bfb224eb96" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/ba0accfa-a634-4f09-860a-61f32182913b" />



🟦 8. Próximos Passos (Futuros)

Ainda não incluídos neste README, conforme solicitado.

Script automático Python → Neo4j

Integração CloudWatch → Lambda → Neo4j

Grafos de eventos em real time

Serviço systemd para monitoramento contínuo

Esses serão adicionados depois como extensões do projeto.

🟦 Conclusão

Este projeto demonstra habilidades práticas nas áreas de:

Suporte Cloud

EC2

Linux

Apache

Observabilidade

CloudWatch (métricas, logs, alarmes, dashboards)

SNS (alertas)

Neo4j (modelagem de grafos)

Segurança de rede

Com ele, você mostra domínio de conceitos fundamentais de Cloud Support e DevOps.

<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/55face8a-0eb3-47bd-a751-ca4a5a77363c" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/c7231153-3d5f-4cb6-ba64-2fc77b127f22" />
<img width="1440" height="900" alt="image" src="https://github.com/user-attachments/assets/d4fbca5d-82b4-46ce-9657-77bfb224eb96" />






