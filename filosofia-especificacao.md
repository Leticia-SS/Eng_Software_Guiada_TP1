# Exercício 3 - Filosofia de Especificar Antes de Codificar

## 1. Explicação da filosofia

A filosofia de "especificar antes de codificar" defende que nenhuma linha de código deve ser gerada antes que o problema esteja claramente compreendido e documentado. Na prática, a especificação funciona como o **modelo de análise** do sistema: ela descreve *o que* o software deve fazer (responsabilidades, atores, regras de negócio, dados e restrições) antes de decidir *como* implementá-lo. Isso é essencial porque a IA generativa não lê pensamentos, ela preenche lacunas com suposições genéricas baseadas em padrões de treino. Se a especificação for vaga, o código gerado será igualmente vago, frágil e distante do contexto real do projeto. Ao escrever a especificação primeiro, o desenvolvedor é forçado a pensar nos casos de borda, nas regras de negócio e nos critérios de aceite, transformando a IA em uma executora de um plano claro, e não em uma adivinhadora. Como vimos nos slides (Etapa 2, p. 6), "quanto mais precisa a especificação, menos a IA precisa adivinhar", e o resultado é um código que atende ao que foi pedido, reduzindo retrabalho e riscos em produção.

## 2. Especificação de análise: notificação por e-mail quando o status de um ticket muda

**Problema:** Atualmente, quando o status de um ticket é alterado (ex.: de "aberto" para "em_andamento" ou para "resolvido"), o cliente não é notificado automaticamente, o que gera ansiedade e aumenta o volume de contatos de suporte perguntando sobre o andamento.

**Atores envolvidos:**
- **Cliente:** usuário que abriu o ticket e que deve receber a notificação.
- **Atendente/Agente de suporte:** profissional que altera o status do ticket.
- **Sistema Nimbus Desk:** responsável por detectar a mudança de status e disparar a notificação.
- **Serviço de e-mail:** canal externo que entrega a mensagem ao cliente.

**Regras de Negócio:**
1. A notificação deve ser enviada sempre que o status de um ticket for alterado para um valor diferente do anterior.
2. A notificação deve ser enviada apenas para o e-mail cadastrado do cliente que abriu o ticket.
3. O e-mail deve conter: número do ticket, título, status anterior, novo status e uma mensagem padrão informando a atualização.
4. Se o cliente não tiver e-mail cadastrado, a notificação não deve ser enviada, e o sistema deve registrar um aviso interno para o atendente.
5. Se o envio do e-mail falhar (ex.: serviço indisponível), o sistema deve tentar reenviar uma vez após 5 minutos; se falhar novamente, registrar o erro em log para análise posterior.
6. A notificação não deve ser enviada se a alteração de status for feita pelo próprio cliente (ex.: cancelamento pelo cliente), apenas se for feita por um atendente.

## 3. Por que validar essa especificação antes de gerar código?

Essa especificação de análise deve ser escrita e validada com o time antes de qualquer prompt de geração de código porque ela captura decisões de negócio que a IA não tem como inferir sozinha. Por exemplo, a regra 6 (não notificar quando o cliente altera o status) é uma decisão de produto que, se não estiver explícita, a IA provavelmente ignoraria, gerando notificações indesejadas. Da mesma forma, a regra 4 (cliente sem e-mail) e a regra 5 (retentativa em caso de falha) são casos de borda que impactam diretamente a experiência do usuário e a confiabilidade do sistema. Validar a especificação com o time, incluindo product managers, atendentes e desenvolvedores, garante que o problema foi compreendido por todas as perspectivas antes de virar código. Isso evita retrabalho, alinha expectativas e transforma a IA em uma ferramenta de execução de um plano validado, e não em uma fonte de suposições arriscadas.
