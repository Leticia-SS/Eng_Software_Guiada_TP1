# Exercício 9 - Critérios de Precisão para Especificações

## 1. Comparação entre as Especificações

A especificação de análise do Exercício 3 é mais geral e descreve o problema, os atores e as regras de negócio da notificação por e-mail. Ela é útil para alinhar o entendimento do time, mas não seria precisa o bastante para gerar código confiável sem intervenção manual significativa, pois não define tipos de dados, formatos de entrada e saída, casos de erro detalhados ou critérios de aceite testáveis.

A especificação estruturada do Exercício 5 é mais detalhada e inclui objetivo, entradas, saídas, regras de negócio numeradas e critérios de aceite verificáveis. Ela seria suficientemente precisa para gerar código confiável com IA, desde que o assistente recebesse o contexto completo e o desenvolvedor revisasse o resultado.

**Conclusão:** Para produção, a especificação estruturada é a mais adequada. A especificação de análise serve como ponto de partida, mas precisa ser complementada com detalhes técnicos e critérios de aceite antes de ser usada para geração de código.

## 2. Critérios Objetivos para uma Especificação Precisa

Proponho os seguintes critérios, cada um justificado com um exemplo retirado das especificações analisadas neste TP.

### Critério 1: Presença de casos de erro explícitos

A especificação deve listar os casos de erro esperados e o comportamento do sistema para cada um. **Exemplo:** No Exercício 3, a Regra 5 define que, se o envio do e-mail falhar, o sistema deve tentar reenviar uma vez após 5 minutos e, se falhar novamente, registrar o erro em log. Sem essa regra, a IA geraria um código que não trata falhas de envio.

### Critério 2: Definição de tipos de dados e formatos de entrada/saída

A especificação deve definir os tipos de dados esperados, os formatos de entrada e saída e as restrições de cada campo. **Exemplo:** No Exercício 5, a seção "Entradas" define que o período de referência é a última semana completa (segunda a domingo anteriores) e que o e-mail deve ser em formato HTML. Sem isso, a IA poderia gerar um relatório em texto puro ou calcular o período de forma incorreta.

### Critério 3: Regras de negócio explícitas e numeradas

A especificação deve listar as regras de negócio de forma clara, numerada e sem ambiguidade. **Exemplo:** No Exercício 5, as Regras 1 a 6 definem desde o período de referência até o assunto do e-mail. Sem essa numeração, a IA poderia ignorar regras ou interpretá-las de forma equivocada.

### Critério 4: Critérios de aceite testáveis

A especificação deve incluir critérios de aceite que possam ser verificados por testes automatizados ou manuais. **Exemplo:** No Exercício 5, os critérios de aceite definem que "dado um cliente com 3 tickets abertos, 2 resolvidos e 1 em andamento, o e-mail deve exibir exatamente esses totais". Isso permite que o desenvolvedor escreva um teste automatizado para validar o comportamento.

### Critério 5: Contexto do projeto e restrições técnicas

A especificação deve incluir o contexto do projeto (tecnologias, padrões, convenções) e as restrições técnicas que a implementação deve respeitar. **Exemplo:** No Exercício 8, a especificação define que o endpoint deve aceitar apenas as transições `aberto → em_andamento` e `em_andamento → resolvido`. Sem essa restrição explícita, a IA geraria um código que aceita qualquer transição.

## 3. Conclusão

Uma especificação precisa o suficiente para gerar código confiável com IA deve atender a pelo menos esses cinco critérios. A especificação de análise do Exercício 3 atende parcialmente ao Critério 3 (regras de negócio) e ao Critério 1 (casos de erro), mas falha nos Critérios 2, 4 e 5. A especificação estruturada do Exercício 5 atende a todos os cinco critérios, sendo a mais adequada para geração de código com IA.
