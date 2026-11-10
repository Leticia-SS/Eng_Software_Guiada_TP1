# Exercício 2 - Comparação: Vibe Coding vs. Spec-First

## 1. Comparação das Implementações

Abaixo, comparo as duas versões geradas a partir dos prompts descritos no enunciado, apontando três diferenças concretas.

| Critério | Vibe Coding (Prompt 1) | Spec-First (Prompt 2) |
| :--- | :--- | :--- |
| **Cobertura de casos de erro** | Nenhuma. O código assume que o ticket sempre existe. Um `ticket_id` inválido causa um `AttributeError` (erro 500), não um 404. Também não valida se o ticket já foi resolvido. | Completa. Trata ticket inexistente (404), ticket já resolvido (400) e status inválido (400), exatamente como especificado. |
| **Organização do código** | Extremamente conciso (4 linhas). Não há separação de responsabilidades nem clareza sobre as regras de negócio. | Mais verboso, mas estruturado com validações em sequência (if/else), tornando as regras de negócio explícitas e fáceis de ler. |
| **Aderência às regras de negócio** | Parcial. Atualiza o status para "resolvido", mas ignora a regra de que o ticket precisa estar "aberto" ou "em_andamento". Também não registra a data de resolução. | Total. Implementa todas as regras: validação de estado, atualização de status, registro de data/hora e ignora o corpo da requisição. |

## 2. Conclusão: Quando Usar Cada Abordagem

O **vibe coding** é aceitável em situações de **exploração rápida e prototipagem**, onde o objetivo é validar uma ideia ou obter um esqueleto funcional para uma demonstração interna. Por exemplo, para criar um mock de uma API para testar a interface do frontend. No entanto, é **inadequado para código de produção** no Nimbus Desk, pois a falta de especificação leva a um código frágil, que não cobre casos de erro e não adere às regras de negócio, acumulando dívida técnica e riscos de falha em produção.

A **especificação estruturada (spec-first)** é **necessária** para qualquer funcionalidade que envolva **regras de negócio, persistência de dados ou que será usada por clientes reais**. Ela força o desenvolvedor a pensar nos casos de borda e nas regras antes de gerar o código, resultando em uma implementação mais robusta, segura e de fácil manutenção, como demonstrado pela cobertura completa de erros e aderência às regras no exemplo.
