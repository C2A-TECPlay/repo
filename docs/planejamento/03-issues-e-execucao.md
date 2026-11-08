# Issues, dependencias e estrategia de execucao

## Distribuicao sugerida

| Integrante | Issues |
|---|---|
| A | #1, #4, #9, #10 |
| B | #3, #7, #8, #11 |
| C | #2, #5, #6, #12 |

Cada pessoa cria, assume e entrega suas quatro issues. Cada issue deve ter descricao, criterios de aceite, responsavel, labels e milestone.

## Quatro ciclos (equipe de tres)

| Ciclo | Ordem de integracao recomendada | Entregas |
|---|---|---|
| 1 | A #1 → C #2 → B #3 | Estrutura Web, cadastro/edicao Web, listagem/exclusao Web. |
| 2 | B #7 → A #4 → C #5 | Estrutura CLI, busca Web, exportacao CSV Web. |
| 3 | B #8 → A #9 → C #6 | Cadastro/edicao CLI, listagem/exclusao CLI, dashboard Web. |
| 4 | A #10 → B #11 → C #12 | Busca CLI, exportacao CSV CLI, indicadores CLI. |

O ciclo seguinte so comeca quando os tres integrantes tiverem integrado uma PR principal no ciclo atual. A ordem acima e recomendada para respeitar dependencias funcionais. Confirmem com o docente se a sequencia definida por ciclo e compativel com o cronograma de apresentacao.

## Dependencias

```text
#1 → #2 → #3
#2 → #4 → #5
#1 → #7 → #8 → #9 → #10 → #11
#2 → #6 → #12
#6 → #12
```

- #1 prepara Compose, autenticacao, API e banco; base para as funcionalidades Web.
- #2 define a persistencia dos campos e permite listar/excluir games reais.
- #4 define busca/filtros usados pela exportacao #5.
- #7 prepara autenticacao/cliente CLI e endpoints CLI iniciais.
- #8 prepara dados e operacoes CLI para #9.
- #9 prepara o mesmo contrato de filtros e listagem para #10.
- #10 precede #11 para a exportacao CLI usar os filtros acordados.
- #12 depende da definicao dos indicadores em #6, mas pode reutilizar o mesmo contrato de banco/API.

## Cartoes das issues

Copiar cada secao para uma issue GitHub. Em cada issue preencher responsavel, milestone e labels (por exemplo `web`, `cli`, `api`, `database`, `tests`, `cycle-1` etc.). Cada PR principal fecha exatamente sua issue com `Closes #N` ou `Fixes #N`.

### #1 — Estrutura Web (A, ciclo 1)

Preparar a base executavel do cadastro de games. Criar/ajustar `index.html`, o arquivo unico da API, o arquivo unico de acesso a dados, Compose e configuracao necessaria. Implementar `GET /api/web/health`, `POST /api/web/login`, usuario inicial configuravel, autenticacao e logs. Documentar como subir a Web e autenticar.

**Aceite:** `docker compose up` inicia Web, API e PostgreSQL; health retorna 200; login valido autentica; login invalido retorna 401; segredos nao sao publicados; README tem comandos; testes aplicaveis e cobertura exigida passam; arquivos auxiliares estao justificados.

### #2 — Cadastro/edicao Web (C, ciclo 1)

Implementar formulario Web para os campos e regras da especificacao. Criar endpoints Web POST/PUT e persistencia no banco. Apresentar erros de validacao de forma clara.

**Aceite:** criar e editar games validos persiste dados; entradas invalidas sao rejeitadas pela API; regras de Web e CLI coincidem; testes/cobertura e README atualizados.

### #3 — Listagem/exclusao Web (B, ciclo 1)

Implementar tabela Web com quatro campos visiveis, cinco itens por pagina, ordenacao, selecao e exclusao. Criar endpoints Web GET/DELETE; carregar dados da API ao abrir a pagina.

**Aceite:** dados persistidos aparecem ao abrir/recarregar; paginacao/ordenacao funcionam; exclusao persiste; API exige autenticacao e registra logs; testes/cobertura e README atualizados.

### #4 — Busca avancada Web (A, ciclo 2)

Implementar busca Web assincrona e `GET /api/web/records/search`, com filtros combinaveis de plataforma, genero e intervalo de ano, opcionalmente busca textual.

**Aceite:** filtros isolados e combinados retornam resultado correto; limpar filtros restaura resultados; paginacao e ordenacao continuam corretas; validacao de intervalo invalido retorna 400; testes/cobertura e README atualizados.

### #5 — Exportacao CSV Web (C, ciclo 2)

Implementar botao e `GET /api/web/records/export`, reaproveitando semantica dos filtros Web.

**Aceite:** CSV valido contem todos os resultados filtrados, nao apenas a pagina; sem filtros exporta todos; caracteres, aspas, virgulas e quebras de linha sao escapados; testes/cobertura e README atualizados.

### #6 — Dashboard Web (C, ciclo 3)

Implementar `GET /api/web/dashboard` e tres graficos Web com valores reais: total de games, media de avaliacoes e quantidade por plataforma.

**Aceite:** graficos usam dados do banco via API, atualizam ao recarregar e incluem plataformas sem registros conforme contrato; testes/cobertura e README definem os indicadores.

### #7 — Estrutura CLI (B, ciclo 2)

Criar `cli.py` com ajuda/subcomandos, `GET /api/cli/health`, `POST /api/cli/token` e autenticacao dos comandos protegidos. Usar variavel de ambiente `GAMES_API_TOKEN` como opcao para token.

**Aceite:** CLI executa pelo Compose; token valido permite chamada protegida; ausencia/token invalido retorna 401; CLI nao acessa banco; ajuda e README documentam uso; testes aplicaveis passam.

### #8 — Cadastro/edicao CLI (B, ciclo 3)

Implementar comandos CLI para criar/editar e endpoints CLI POST/PUT, usando os mesmos campos e validacoes da Web.

**Aceite:** validacao local oferece erro compreensivel; API revalida; registros persistem e aparecem na Web; testes/cobertura e README atualizados.

### #9 — Listagem/exclusao CLI (A, ciclo 3)

Implementar comandos CLI de listagem, paginacao, ordenacao e exclusao por id; criar endpoints CLI GET/DELETE proprios.

**Aceite:** alteracoes aparecem na Web; CLI chama apenas API; entradas invalidas/inexistentes recebem erro coerente; logs, testes/cobertura e README atualizados.

### #10 — Busca avancada CLI (A, ciclo 4)

Implementar busca com pelo menos tres filtros combinaveis e `GET /api/cli/records/search`.

**Aceite:** mesmos parametros produzem resultados equivalentes a busca Web; validacao de entrada e paginacao coincidem; logs, testes/cobertura e README atualizados.

### #11 — Exportacao CSV CLI (B, ciclo 4)

Implementar comando CLI de exportacao e `GET /api/cli/records/export`.

**Aceite:** exporta todos os resultados filtrados, independentemente da pagina; documenta caminho/nome de arquivo; CSV segue mesmo contrato da Web; testes/cobertura e README atualizados.

### #12 — Indicadores CLI (C, ciclo 4)

Implementar comando CLI e `GET /api/cli/dashboard`.

**Aceite:** mostra os mesmos tres indicadores e valores da Web; resultados sao calculados a partir de dados reais; logs, testes/cobertura e README atualizados.

## Procedimento de cada PR principal

1. Agente responsavel trabalha em fork proprio e worktree/branch da issue.
2. Antes de codificar, verifica no `main` integrado os contratos e dependencias da issue.
3. PR aponta para `main` do repositorio principal, usa template, referencia issue e lista arquivos auxiliares com justificativa.
4. Dois colegas revisam e aprovam; o autor nao conta como aprovador.
5. Integrar exclusivamente com **Rebase and merge**. Nao fazer commit direto em `main`, merge commit, squash merge ou force push em `main`.
6. Apos cada PR principal, criar tag SemVer unica/crescente apontando para commit de `main` apos merge e release associada a issue/PR.
7. PR corretiva usa `Refs #N`, justifica o motivo, recebe aprovacao dos colegas e nao conta no ciclo nem exige tag/release.

## Paralelizacao segura

- Paralelizar especificacao, testes de contrato, documentacao, UI em `index.html` e CLI em `cli.py` quando os contratos estiverem congelados.
- `Program.cs` e `Db.cs` sao arquivos compartilhados. Um unico agente deve ter permissao de escrita sobre esses arquivos por vez, ou as mudancas devem ser coordenadas em trechos/rotas sem editar simultaneamente.
- PRs podem ser preparadas em paralelo; merges principais respeitam ordem, ciclo, dependencias e aprovacao.
