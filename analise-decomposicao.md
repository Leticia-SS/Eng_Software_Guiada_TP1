# Exercício 6 - Auditoria da Decomposição de Tarefas

## 1. Análise das Tarefas: O que veio da Spec vs. O que a IA inventou

Abaixo, analiso cada um dos 6 passos propostos pela IA no Exercício 5, verificando sua correspondência com a especificação estruturada.

| Passo da IA | Tarefa Proposta | Vínculo com a Especificação |
| :--- | :--- | :--- |
| 1 | Consulta ao banco de dados para buscar tickets do período | **Direto.** Corresponde à seção "Entradas" (base de tickets e período de referência) e à Regra de Negócio 1. |
| 2 | Agregação dos dados por cliente (contagem por status) | **Direto.** Corresponde à seção "Saídas" (tabela com totais por status). |
| 3 | Formatação do relatório em HTML | **Direto.** Corresponde à seção "Saídas" (e-mail em formato HTML com tabela). |
| 4 | Envio de e-mail para cada cliente | **Direto.** Corresponde à seção "Saídas" (e-mail enviado para clientes com tickets) e à Regra de Negócio 2. |
| 5 | Agendamento da execução semanal (cron) | **Sem correspondência clara.** A especificação menciona "toda segunda-feira às 08:00" como objetivo, mas não define o agendamento como uma tarefa de implementação separada. A IA assumiu que seria um cron, mas não foi especificado. |
| 6 | Registro de logs de execução | **Sem correspondência clara.** A especificação menciona "log de execução" na seção "Saídas", mas não define quais eventos logar, o formato ou o nível de detalhe. A IA inseriu como um passo genérico. |

**Conclusão da auditoria:** As tarefas 1 a 4 decorrem diretamente da especificação. As tarefas 5 e 6 foram inseridas pela IA sem correspondência clara, pois a especificação não as detalhava como etapas de implementação. Isso é um exemplo do que os slides chamam de "lacunas preenchidas por suposições" (Slides Etapa 3, p. 60).

## 2. Reescrevendo a Especificação para Cobrir as Lacunas

Para eliminar as suposições da IA, reescrevo as seções da especificação que deveriam ter previsto as tarefas 5 e 6, deixando explícito o vínculo entre especificação e decomposição.

### Seção "Entradas" (adicionada)

> **Agendador de tarefas:** O sistema deve utilizar um agendador (cron ou similar) configurado para disparar o processo toda segunda-feira às 08:00. O agendador deve ser versionado junto ao código e sua configuração deve ser testável em ambiente de desenvolvimento.

### Seção "Saídas" (reescrita)

> **Log de execução:** O sistema deve registrar em log, ao final de cada execução, os seguintes eventos: (a) total de tickets encontrados no período; (b) total de clientes com tickets; (c) total de e-mails enviados com sucesso; (d) total de falhas de envio (com o motivo); (e) total de clientes sem e-mail cadastrado. O log deve ser estruturado em formato JSON e gravado em arquivo diário.

### Seção "Regras de Negócio" (adicionada)

> **Regra 7 (Agendamento):** O processo deve ser idempotente: se for executado mais de uma vez no mesmo dia, não deve enviar e-mails duplicados para os clientes. O controle de idempotência deve ser feito com base na data de execução e no cliente.

**Vínculo explícito:** Com essas adições, as tarefas 5 (agendamento) e 6 (logs) passam a decorrer diretamente da especificação, e a IA não precisa mais preencher lacunas com suposições. A decomposição fica alinhada ao que foi validado com o time.
