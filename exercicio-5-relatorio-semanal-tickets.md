# Exercício 5 - Especificação e Decomposição: Relatório Semanal de Tickets por Cliente

## 1. Especificação Estruturada

**Objetivo:** Gerar e enviar automaticamente, toda segunda-feira às 08:00, um relatório semanal consolidando os tickets de cada cliente do Nimbus Desk, permitindo que os clientes acompanhem o volume e o status dos seus atendimentos.

### Entradas

- Base de dados de tickets (campos: id, client_id, status, data_criacao, data_resolucao).
- Base de dados de clientes (campos: client_id, nome, email).
- Período de referência: última semana completa (segunda a domingo anteriores).
- Agendador de tarefas (cron ou similar) que dispara o processo toda segunda-feira às 08:00.

### Saídas

- Um e-mail em formato HTML enviado para cada cliente que teve pelo menos um ticket no período.
- O e-mail deve conter uma tabela com: total de tickets abertos, total de tickets resolvidos e total de tickets em andamento na semana.
- Um log de execução registrando quantos e-mails foram enviados, quantos falharam e quantos clientes não tinham tickets no período.

### Regras de Negócio

1. O relatório deve considerar apenas tickets criados ou atualizados dentro do período de referência (última semana completa).
2. Clientes sem nenhum ticket no período não devem receber e-mail (evitar spam).
3. Se o envio do e-mail falhar para um cliente, o sistema deve tentar reenviar uma vez após 10 minutos; se falhar novamente, registrar o erro em log e continuar o processamento para os demais clientes (falha isolada não pode interromper o lote).
4. O e-mail deve ser enviado apenas para clientes com e-mail cadastrado e válido.
5. O relatório deve ser gerado mesmo que não haja tickets no período, mas nesse caso nenhum e-mail é enviado (apenas log).
6. O assunto do e-mail deve ser: "Relatório Semanal Nimbus Desk - [nome do cliente] - [data de início] a [data de fim]".

### Critérios de Aceite

- Dado um cliente com 3 tickets abertos, 2 resolvidos e 1 em andamento na semana, o e-mail deve exibir exatamente esses totais.
- Dado um cliente sem tickets na semana, nenhum e-mail deve ser enviado para ele.
- Dado um cliente com e-mail inválido, o sistema deve registrar erro em log e não interromper o envio para os demais clientes.
- O processo deve ser executado automaticamente toda segunda-feira às 08:00, sem intervenção manual.
- O log de execução deve conter o total de e-mails enviados, falhas e clientes sem tickets.

---

## 2. Decomposição em Tarefas (Prompting Estruturado)

Apliquei a técnica de decomposição de requisitos (Slides Etapa 2, p. 18-20), pedindo primeiro o plano e depois cada tarefa separadamente. A decomposição resultou em 4 tarefas principais, conforme solicitado.

### Prompt 1 (Pedido do Plano)

> "Você é um engenheiro de software sênior. Com base na especificação abaixo, liste em passos numerados as subtarefas para implementar o relatório semanal de tickets por cliente. Cada passo deve gerar uma única função ou módulo. Não escreva código ainda. Especificação: [colar a especificação completa do item 1]."

**Resultado obtido (decomposição sugerida pela IA):**

1. Consulta ao banco de dados para buscar tickets do período e clientes correspondentes.
2. Agregação dos dados por cliente (contagem por status).
3. Formatação do relatório em HTML.
4. Envio de e-mail para cada cliente com relatório.
5. Agendamento da execução semanal (cron).
6. Registro de logs de execução.

**Nota de revisão:** A IA inseriu os passos 5 e 6 (agendamento e logs) por conta própria, pois não estavam explícitos na especificação como tarefas separadas. Isso será analisado no Exercício 6 (auditoria da decomposição). Para este exercício, detalho as 4 tarefas centrais (passos 1 a 4), que são as que decorrem diretamente da especificação.

---

## 3. Registro dos Prompts e Resultados por Etapa

### Tarefa 1 - Consulta ao Banco

**Prompt 2:**

> "Agora implemente apenas o passo 1: uma função Python buscar_tickets_periodo(data_inicio, data_fim) que consulta o banco de dados e retorna uma lista de tickets criados ou atualizados no período. Use SQLAlchemy. Entregue o código e 2 testes com assert."

**Resultado obtido:**

```python
from datetime import datetime
from models import Ticket

def buscar_tickets_periodo(data_inicio, data_fim):
    return Ticket.query.filter(
        (Ticket.data_criacao >= data_inicio) |
        (Ticket.data_atualizacao >= data_inicio)
    ).filter(
        (Ticket.data_criacao <= data_fim) |
        (Ticket.data_atualizacao <= data_fim)
    ).all()
```

(A IA também gerou dois testes com assert, que foram revisados e aceitos.)

---

### Tarefa 2 - Agregação por Cliente

**Prompt 3:**

> "Agora implemente apenas o passo 2: uma função Python agregar_por_cliente(tickets) que recebe a lista de tickets e retorna um dicionário no formato {client_id: {'abertos': X, 'resolvidos': Y, 'em_andamento': Z}}. Entregue o código e 2 testes com assert."

**Resultado obtido:**

```python
def agregar_por_cliente(tickets):
    agregado = {}
    for ticket in tickets:
        if ticket.client_id not in agregado:
            agregado[ticket.client_id] = {'abertos': 0, 'resolvidos': 0, 'em_andamento': 0}
        if ticket.status == 'aberto':
            agregado[ticket.client_id]['abertos'] += 1
        elif ticket.status == 'resolvido':
            agregado[ticket.client_id]['resolvidos'] += 1
        elif ticket.status == 'em_andamento':
            agregado[ticket.client_id]['em_andamento'] += 1
    return agregado
```

(Testes gerados e revisados.)

---

### Tarefa 3 - Formatação do Relatório em HTML

**Prompt 4:**

> "Agora implemente apenas o passo 3: uma função Python formatar_relatorio_html(cliente, agregado, data_inicio, data_fim) que retorna uma string HTML com o relatório formatado. O HTML deve conter uma tabela com os totais de tickets abertos, resolvidos e em andamento. Entregue o código e 1 teste com assert que verifique se o HTML contém os valores corretos."

**Resultado obtido:**

```python
def formatar_relatorio_html(cliente, agregado, data_inicio, data_fim):
    return f"""
<html>
<body>
<h2>Relatório Semanal - {cliente.nome}</h2>
<p>Período: {data_inicio.strftime('%d/%m/%Y')} a {data_fim.strftime('%d/%m/%Y')}</p>
<table border="1">
<tr><th>Status</th><th>Total</th></tr>
<tr><td>Abertos</td><td>{agregado['abertos']}</td></tr>
<tr><td>Resolvidos</td><td>{agregado['resolvidos']}</td></tr>
<tr><td>Em Andamento</td><td>{agregado['em_andamento']}</td></tr>
</table>
</body>
</html>
"""
```

(Teste gerado e revisado.)

---

### Tarefa 4 - Envio de E-mail

**Prompt 5:**

> "Agora implemente apenas o passo 4: uma função Python enviar_email_relatorio(cliente, html) que envia o e-mail para o cliente usando smtplib. A função deve tratar falha no envio com uma retentativa após 10 minutos, conforme a regra de negócio 3. Entregue o código e 1 teste com mock para simular falha."

**Resultado obtido:**

```python
import smtplib
import time
import logging
from email.mime.text import MIMEText

logger = logging.getLogger(__name__)

def enviar_email_relatorio(cliente, html):
    if not cliente.email:
        logger.warning(f"Cliente {cliente.id} sem e-mail. Relatório não enviado.")
        return False

    msg = MIMEText(html, "html")
    msg["Subject"] = f"Relatório Semanal Nimbus Desk - {cliente.nome}"
    msg["From"] = "noreply@nimbusdesk.com"
    msg["To"] = cliente.email

    for tentativa in range(2):
        try:
            with smtplib.SMTP("smtp.nimbusdesk.com", 587) as server:
                server.send_message(msg)
            logger.info(f"Relatório enviado para {cliente.nome}")
            return True
        except Exception as e:
            if tentativa == 0:
                logger.warning(f"Falha no envio para {cliente.nome}. Retentando em 10 min...")
                time.sleep(600)
            else:
                logger.error(f"Falha definitiva para {cliente.nome}: {e}")
                return False
```

(Teste com mock gerado e revisado.)
