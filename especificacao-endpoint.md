# Exercício 1 - Especificação e Geração do Endpoint GET /tickets

## 1. Especificação para a IA

A especificação enviada ao assistente de IA foi:

> "Crie um endpoint em Python com Flask chamado GET /tickets. Ele deve retornar uma lista JSON com os tickets abertos de um cliente autenticado. A autenticação é feita via token no header Authorization. O endpoint aceita um parâmetro de query opcional chamado `status`. Se o filtro for fornecido, retorne apenas os tickets com aquele status exato. Caso o cliente não tenha tickets, retorne uma lista vazia com status 200. Se o token for inválido ou ausente, retorne 401. Se o status fornecido não existir, retorne 400 com uma mensagem de erro."

## 2. Mapeamento: Especificação vs. Código Gerado

**Código gerado pelo assistente de IA (após revisão):**

```python
from flask import Flask, request, jsonify

app = Flask(__name__)

# Simulação de um banco de dados de tickets e clientes
TICKETS_DB = [
    {"id": 1, "client_id": "abc", "status": "aberto", "title": "Problema no login"},
    {"id": 2, "client_id": "abc", "status": "em_andamento", "title": "Erro no pagamento"},
    {"id": 3, "client_id": "xyz", "status": "aberto", "title": "Dúvida sobre funcionalidade"}
]

@app.route("/tickets", methods=["GET"])
def get_tickets():
    # [Parte complementada manualmente]
    # Validação da autenticação (simplificada para o exemplo)
    auth_header = request.headers.get("Authorization")
    if not auth_header or not auth_header.startswith("Bearer "):
        return jsonify({"erro": "Token de autenticação ausente ou inválido"}), 401
    
    # Simulação de extração do client_id do token
    client_id = "abc" # Em produção, seria decodificado do token JWT
    
    # [Mapeado diretamente da especificação]
    status_filter = request.args.get("status")
    
    # [Mapeado diretamente da especificação]
    tickets_abertos = [t for t in TICKETS_DB if t["client_id"] == client_id and t["status"] == "aberto"]
    
    # [Mapeado diretamente da especificação]
    if status_filter:
        # [Parte complementada manualmente: validação se o status é válido]
        status_validos = ["aberto", "em_andamento", "resolvido"]
        if status_filter not in status_validos:
            return jsonify({"erro": f"Status '{status_filter}' inválido"}), 400
        tickets_filtrados = [t for t in tickets_abertos if t["status"] == status_filter]
    else:
        tickets_filtrados = tickets_abertos
        
    return jsonify(tickets_filtrados), 200
