# Contrato da API

Este contrato e compartilhado pelas interfaces Web e CLI. As rotas correspondentes sao distintas; nao substituir uma rota `/api/web/...` pela rota `/api/cli/...` ou vice-versa.

## Convencoes

- Base local: `http://localhost:8080` (ajustar uma unica vez ao definir o mapeamento de portas do Compose).
- JSON em UTF-8; `Content-Type: application/json`.
- Rotas funcionais exigem `Authorization: Bearer <token>`.
- Identificadores sao inteiros positivos.
- Erros usam formato uniforme: `{"error":{"code":"validation_error","message":"...","details":{"field":"..."},"request_id":"..."}}`. Nao incluir credenciais, tokens ou SQL.
- Sucesso de leitura/criacao/edicao retorna JSON; exclusao bem-sucedida retorna HTTP 204.
- `id` e gerado pelo banco. Campos de entrada nao aceitam `id`.
- Ano valido: 1970 ate ano UTC atual + 1.

## Autenticacao e healthcheck

| Metodo e rota | Auth | Entrada | Resposta |
|---|---|---|---|
| `GET /api/web/health` | Publica | — | `200 {"status":"ok"}` |
| `GET /api/cli/health` | Publica | — | `200 {"status":"ok"}` |
| `POST /api/web/login` | Publica | `{"username":"...","password":"..."}` | `200 {"access_token":"...","token_type":"Bearer","expires_in":3600}`; credenciais invalidas: 401. |
| `POST /api/cli/token` | Publica | `{"username":"...","password":"..."}` | Mesmo formato de token; credenciais invalidas: 401. |

Login Web e emissao de token CLI sao rotas separadas para atender a avaliacao. Token Web e CLI podem usar o mesmo mecanismo interno. Todas as demais rotas abaixo exigem bearer token.

## Modelo JSON

```json
{
  "id": 17,
  "title": "Hollow Knight",
  "platform": "PC",
  "genre": "Aventura",
  "release_year": 2017,
  "rating": 9.2,
  "notes": null
}
```

Valores canonicos de `platform`: `PC`, `PlayStation`, `Xbox`, `Switch`, `Mobile`, `Outro`.

Valores canonicos de `genre`: `Ação`, `Aventura`, `RPG`, `Estratégia`, `Esporte`, `Corrida`, `Puzzle`, `Outro`. Os valores sao transmitidos em UTF-8 com acentuacao.

## Rotas CRUD Web e CLI

| Operacao | Web | CLI | Entrada/resultado |
|---|---|---|---|
| Listar | `GET /api/web/records` | `GET /api/cli/records` | Query de filtros/paginacao abaixo. `200 {"items":[Game...],"page":1,"page_size":5,"total":12}`. |
| Criar | `POST /api/web/records` | `POST /api/cli/records` | Body sem `id`; `201` com objeto Game criado. |
| Editar | `PUT /api/web/records/{id}` | `PUT /api/cli/records/{id}` | Body completo sem `id`; `200` com Game atualizado. |
| Excluir | `DELETE /api/web/records/{id}` | `DELETE /api/cli/records/{id}` | `204`; id inexistente: 404. |
| Buscar | `GET /api/web/records/search` | `GET /api/cli/records/search` | Query de filtros/paginacao; mesmo formato paginado de listagem. |
| CSV | `GET /api/web/records/export` | `GET /api/cli/records/export` | `200 text/csv; charset=utf-8`, todos os resultados filtrados, sem paginacao. |
| Indicadores | `GET /api/web/dashboard` | `GET /api/cli/dashboard` | `200` no formato abaixo, com mesmos valores nas duas rotas. |

O endereco `GET /records/search` e uma rota propria, nao uma query opcional da rota de listagem. A API pode compartilhar logica interna, mas mantem os endpoints requeridos e os prefixos separados.

## Rotas extras (nao aplicaveis a equipe de tres)

| Operacao | Web | CLI | Resultado |
|---|---|---|---|
| Alterar status | `PATCH /api/web/records/{id}/status` | `PATCH /api/cli/records/{id}/status` | Body `{"active":true}`; atualizar e retornar o registro. |
| Duplicar | `POST /api/web/records/{id}/duplicate` | `POST /api/cli/records/{id}/duplicate` | `201` com novo `id`, demais campos copiados; original inalterado. |

## Filtros, paginacao e ordenacao

Parametros opcionais aceitos em listagem e busca:

| Parametro | Regra |
|---|---|
| `platform` | Um valor canonico de plataforma. |
| `genre` | Um valor canonico de genero. |
| `release_year_from` | Inteiro >= 1970; limite inclusivo. |
| `release_year_to` | Inteiro <= ano atual + 1; limite inclusivo. |
| `q` | Busca parcial sem diferenciar caixa em titulo e notas. |
| `page` | Inteiro >= 1; padrao 1. |
| `page_size` | Inteiro de 1 a 100; padrao 5. |
| `sort_by` | `title`, `platform`, `genre`, `release_year` ou `rating`; padrao `title`. |
| `sort_order` | `asc` ou `desc`; padrao `asc`. |

`release_year_from > release_year_to` retorna 400. Valores de enumeracao desconhecidos, parametros numericos malformados e ordenacao fora da lista permitida retornam 400. O SQL de ordenacao deve usar lista permitida, nunca concatenar entrada livre. A resposta paginada inclui `items`, `page`, `page_size` e `total`.

## Dashboard

```json
{
  "total_games": 12,
  "average_rating": 8.35,
  "games_by_platform": [
    {"platform":"PC","count":4},
    {"platform":"PlayStation","count":3},
    {"platform":"Xbox","count":2},
    {"platform":"Switch","count":1},
    {"platform":"Mobile","count":1},
    {"platform":"Outro","count":1}
  ]
}
```

`average_rating` e arredondada a duas casas. Plataformas sem registros aparecem com `count: 0`. Total zero produz media `0.00`, nao `null`. A CLI apresenta os mesmos tres indicadores.

## Status HTTP

- `200`: leitura, edicao, login/token bem-sucedido.
- `201`: criacao e duplicacao.
- `204`: exclusao bem-sucedida.
- `400`: entrada malformada ou validacao falhou.
- `401`: ausente/invalida autenticacao ou credenciais.
- `404`: game inexistente.
- `500`: falha inesperada; resposta generica com `request_id`, detalhe interno somente em log seguro.

## CSV

Cabecalho, nesta ordem: `id,title,platform,genre,release_year,rating,notes`. Usar escaping CSV correto, UTF-8 e CRLF ou LF consistentemente. Aplicar os filtros exatamente como na busca; ignorar `page` e `page_size`. Nome sugerido para download: `games.csv`.

## Logs e correlacao

Middleware da API atribui/valida um `request_id` por requisicao e o inclui nas linhas de API e DB. Nao aceitar valor arbitrario ilimitado: se receber `X-Request-Id`, limitar tamanho/formato ou gerar novo identificador. As chamadas DB recebem explicitamente o mesmo id. Cada evento segue o schema definido em `01-spec.md`.
