# Exercício 8 - Revisão de Código

## 1. Código Original 

```python
@app.route("/tickets/<int:ticket_id>/status", methods=["PATCH"])
def update_status(ticket_id):
    novo_status = request.json.get("status")
    ticket = Ticket.query.get(ticket_id)
    ticket.status = novo_status
    db.session.commit()
    return jsonify(ticket.to_dict())
```

**Especificação combinada:** O endpoint `PATCH /tickets/{id}/status` deve aceitar apenas as transições `aberto → em_andamento` e `em_andamento → resolvido`, recusando qualquer outra transição com erro 400 e mensagem explicativa.

---

## 2. Lacunas Identificadas

| # | Lacuna | Descrição |
|---|--------|-----------|
| 1 | Ausência de validação da transição de status | O código aceita qualquer valor em `novo_status` e o atribui diretamente ao ticket, sem verificar se a transição é permitida pela especificação (`aberto → em_andamento` ou `em_andamento → resolvido`). Uma transição como `aberto → resolvido` ou `resolvido → aberto` seria aceita, violando a regra de negócio. |
| 2 | Ausência de tratamento para ticket inexistente | Se `Ticket.query.get(ticket_id)` retornar `None`, o código quebra com `AttributeError` ao tentar acessar `ticket.status`. O esperado é retornar 404 com mensagem explicativa. |
| 3 | Ausência do retorno de erro 400 com mensagem explicativa | A especificação exige que transições inválidas sejam recusadas com erro 400 e mensagem explicativa. O código não retorna erro algum; ele simplesmente aceita a transição. |
| 4 | Ausência de validação do corpo da requisição | O código assume que `request.json` existe e contém a chave `status`. Se o corpo for vazio ou não tiver a chave, `novo_status` será `None`, e o código atribuirá `None` ao status do ticket, corrompendo os dados. |
| 5 | Ausência de validação do valor de `novo_status` | Mesmo que a transição seja válida, o código não verifica se `novo_status` é um valor permitido (`em_andamento` ou `resolvido`). Um valor como `"cancelado"` seria aceito. |

---

## 3. Código Corrigido

```python
from flask import request, jsonify
from datetime import datetime

# Mapeamento das transições permitidas
TRANSICOES_PERMITIDAS = {
    "aberto": ["em_andamento"],
    "em_andamento": ["resolvido"]
}

@app.route("/tickets/<int:ticket_id>/status", methods=["PATCH"])
def update_status(ticket_id):
    # Lacuna 4: validar corpo da requisição
    if not request.json or "status" not in request.json:
        return jsonify({"erro": "Corpo da requisição inválido. Campo 'status' é obrigatório."}), 400

    novo_status = request.json["status"]

    # Lacuna 2: tratar ticket inexistente
    ticket = Ticket.query.get(ticket_id)
    if not ticket:
        return jsonify({"erro": f"Ticket {ticket_id} não encontrado."}), 404

    # Lacuna 1 e 3: validar transição permitida
    status_atual = ticket.status
    transicoes_validas = TRANSICOES_PERMITIDAS.get(status_atual, [])

    if novo_status not in transicoes_validas:
        return jsonify({
            "erro": f"Transição inválida: '{status_atual}' → '{novo_status}'. "
                    f"Transições permitidas a partir de '{status_atual}': {transicoes_validas}"
        }), 400

    # Lacuna 5: já coberta pela validação de transição
    ticket.status = novo_status
    ticket.data_atualizacao = datetime.utcnow()
    db.session.commit()

    return jsonify(ticket.to_dict()), 200
```

---

## 4. Explicação das Correções

**Lacuna 1 (validação da transição):** Adicionei o dicionário `TRANSICOES_PERMITIDAS`, que mapeia cada status atual para a lista de status que ele pode assumir. O código verifica se `novo_status` está nessa lista antes de permitir a alteração.

**Lacuna 2 (ticket inexistente):** Adicionei a verificação `if not ticket: return jsonify(...), 404`. Sem isso, o código quebraria ao tentar acessar `ticket.status` em um objeto `None`.

**Lacuna 3 (retorno de erro 400):** O código agora retorna `jsonify({"erro": ...}), 400` quando a transição é inválida, com uma mensagem explicativa que informa o status atual, o status solicitado e as transições permitidas.

**Lacuna 4 (corpo da requisição):** Adicionei a verificação `if not request.json or "status" not in request.json`, que retorna 400 se o corpo for vazio ou não tiver a chave `status`.

**Lacuna 5 (valor de `novo_status`):** A validação de transição já cobre isso indiretamente, pois `TRANSICOES_PERMITIDAS` só contém valores válidos (`em_andamento` e `resolvido`).

**Bônus:** Adicionei também a atualização do campo `data_atualizacao`, que é uma boa prática para rastreabilidade e não estava no código original.
