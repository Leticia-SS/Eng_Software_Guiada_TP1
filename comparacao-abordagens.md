# Exercício 2 - Comparação: Vibe Coding vs. Spec-First

## Prompt 1 (Vibe Coding)

> "cria um endpoint para resolver um ticket"

## Código Gerado (Vibe Coding)

```python
@app.route("/tickets/<int:ticket_id>/resolver", methods=["PATCH"])
def resolver_ticket(ticket_id):
    ticket = Ticket.query.get(ticket_id)
    ticket.status = "resolvido"
    db.session.commit()
    return jsonify(ticket.to_dict())
