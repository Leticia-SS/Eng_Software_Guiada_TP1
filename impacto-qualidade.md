# Exercício 4 - Impacto da Abordagem Spec-First na Qualidade do Código

## 1. Comparação das Duas Versões

Abaixo, comparo as duas implementações geradas, com base na especificação de análise do Exercício 3.

| Critério | Versão Spec-First | Versão sem Spec (instrução curta) |
| :--- | :--- | :--- |
| **Tratamento de erros** | Completo. Trata falha no envio de e-mail com retentativa (uma vez após 5 minutos) e registra erro em log caso a falha persista. Também trata o caso de cliente sem e-mail cadastrado, registrando um aviso interno. | Inexistente. O código assume que o e-mail do cliente sempre existe e que o servidor SMTP sempre está disponível. Qualquer falha (e-mail nulo, servidor fora do ar) causa uma exceção não tratada, quebrando a aplicação. |
| **Cobertura das regras de negócio** | Total. Implementa todas as 6 regras da especificação: verifica se o status mudou (regra 1), checa e-mail do cliente (regra 2), monta mensagem com os dados exigidos (regra 3), trata ausência de e-mail (regra 4), implementa retentativa (regra 5) e não notifica se o cliente alterou (regra 6). | Parcial. Notifica a mudança de status (regra 1, de forma implícita), mas ignora todas as demais: não verifica se o status realmente mudou, não valida e-mail, não monta mensagem com os dados exigidos, não trata ausência de e-mail, não implementa retentativa e não diferencia quem alterou o status. |
| **Facilidade de manutenção** | Alta. O código é estruturado com validações em sequência, comentários que mapeiam cada regra de negócio e uso de `logging` para rastreabilidade. É fácil entender o que cada bloco faz e alterar uma regra específica sem afetar as demais. | Baixa. O código é uma função de 6 linhas sem validações, sem comentários e sem separação de responsabilidades. Qualquer alteração (ex.: adicionar uma regra) exige reescrever a função quase por completo, e não há logs para diagnosticar falhas. |

## 2. Conclusão

A abordagem **spec-first** produziu código de **maior qualidade** porque a especificação de análise forneceu à IA o contexto necessário para implementar as regras de negócio, os casos de borda e o tratamento de erros. Isso resultou em um código robusto, testável e alinhado ao problema real do Nimbus Desk.

A versão gerada a partir de uma instrução curta e informal é um exemplo claro do **problema dos 70%** (Slides Etapa 2, p. 44): a IA entregou rapidamente um código que "funciona no caminho feliz", mas que ignora completamente os 30% finais, casos de borda, tratamento de erros e aderência às regras de negócio. Em produção, esse código falharia na primeira vez que um cliente não tivesse e-mail cadastrado ou que o servidor SMTP estivesse indisponível.

Portanto, a especificação não é um luxo: é o que transforma a IA de uma geradora de rascunhos frágeis em uma executora de soluções confiáveis.
