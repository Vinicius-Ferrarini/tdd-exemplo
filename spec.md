---
title: "Spec — Sistema de Reservas de Quadras"
type: knowledge
status: done
area: resources
resource: talks
tags:
  - kind/knowledge
  - area/resources
  - resource/talks
  - status/done
created: 2026-10-07
updated: 2026-10-07
---
# Spec — Sistema de Reservas de Quadras

## Rotas

| Método | Rota | Sucesso | Erros |
| --- | --- | --- | --- |
| `POST` | `/courts` | `201` + quadra | `400` |
| `GET` | `/courts` | `200` + array | — |
| `POST` | `/clients` | `201` + cliente | `400` |
| `GET` | `/clients` | `200` + array | — |
| `POST` | `/bookings` | `201` + reserva com preço | `400`, `404` (quadra), `409` |
| `GET` | `/bookings?courtId=&date=YYYY-MM-DD` | `200` + array | `400` (filtro inválido) |
| `GET` | `/bookings/{id}` | `200` | `404` (cancelada/inexistente) |
| `DELETE` | `/bookings/{id}` | `204` | `404` (cancelada/inexistente) |

Erros sempre no formato `{"detail": "mensagem"}`. Campos extras no corpo são ignorados.

## Casos de uso

**UC1 — Cadastrar quadra** (`POST /courts`)
- Entrada: `name` (string, obrigatório), `type` (`futebol` | `vôlei` | `basquete`, opcional, padrão `futebol`), `pricePerHour` (número > 0, obrigatório).
- Resposta: `{"id": 1, "name": "Quadra 1", "type": "futebol", "pricePerHour": 100.0}`
- Critérios de aceite:
  - Quando `pricePerHour` for menor ou igual a zero ou não for enviado, o sistema deve responder 400.
  - Quando `type` não for um dos três valores, o sistema deve responder 400.
  - `GET /courts` lista todas as quadras.

**UC2 — Cadastrar cliente** (`POST /clients`)
- Entrada: `name` (obrigatório) e `telefone` (obrigatório). O campo `phone` também é aceito no lugar de `telefone`.
- Resposta: `{"id": 1, "name": "Maria", "telefone": "44 99999-0000", "phone": "44 99999-0000"}`
- Critérios de aceite:
  - Quando não vier telefone, o sistema deve responder 400.
  - `GET /clients` lista todos os clientes, com `telefone`.

**UC3 — Criar reserva** (`POST /bookings`)
- Entrada: `courtId` (inteiro), `clientId` (texto ou número, guardado como veio; não precisa ser cliente cadastrado), `start` e `end` (ISO 8601).
- Resposta: `{"id": 1, "courtId": 1, "clientId": "cliente-1", "start": "...", "end": "...", "price": 200.0, "status": "active"}`
- Critérios de aceite, verificados nesta ordem:
  - Quando faltar campo ou algum valor tiver formato inválido, o sistema deve responder 400.
  - Quando a quadra não existir, o sistema deve responder 404.
  - Quando `end` for menor ou igual a `start`, o sistema deve responder 400.
  - A duração deve ser de **no mínimo 1h e no máximo 3h**; exatamente 1h e exatamente 3h são aceitas. Fora disso, 400.
  - Reservas com `start` no passado devem ser rejeitadas (400).
  - Se a quadra já tiver reserva ativa que **se sobrepõe** ao intervalo, 409. Reservas encostadas não se sobrepõem (uma termina 11:00 e a outra começa 11:00).
  - Preço = `pricePerHour × horas`, arredondando a hora para cima: 1h → 1×, 1h01 → 2×, 1h30 → 2×, 3h → 3×.

**UC4 — Consultar e cancelar reserva**
- `GET /bookings/{id}` devolve a reserva; cancelada ou inexistente → 404.
- `DELETE /bookings/{id}` cancela (204); cancelada ou inexistente → 404.
- A reserva cancelada não aparece em nenhuma listagem e libera o horário para outra reserva.

**UC5 — Listar reservas** (`GET /bookings`)
- Por quadra: `?courtId={id}`. Por dia: `?date=YYYY-MM-DD` (reservas que tocam o dia; uma reserva das 23:00 à 01:00 aparece nos dois dias).
- Só reservas ativas, ordenadas por `start`. Os dois filtros podem ser usados juntos. Sem resultado, `[]`.
- Filtro com formato inválido → 400.

## Datas

- Aceitar com ou sem fuso. Com fuso, converter para o horário local e remover o fuso; sem fuso, usar como veio.
- "Passado" é comparado com `datetime.now()`. Na resposta, datas no formato `2026-11-05T10:00:00`.
