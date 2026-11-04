# Check-list 5S e Tratativa de Avarias – Donos de Rua

App web (celular e tablet) para o check-list diário 5S dos donos de rua: o colaborador preenche e assina, o líder confere e assina, e tudo fica no histórico por 12 meses, com PDF semanal e Excel.

## Como subir (GitHub + Vercel + Upstash)

1. **GitHub:** crie um repositório (ex.: `checklist-5s`) e envie todos os arquivos desta pasta.
2. **Vercel:** *Add New → Project*, importe o repositório. Não precisa mudar nada (sem framework, sem build). Clique em *Deploy*.
3. **Banco:** no projeto da Vercel, aba *Storage → Create / Connect Database → Upstash (Redis)*. Pode criar um banco novo ou conectar um existente. A Vercel cria sozinha as variáveis `KV_REST_API_URL` e `KV_REST_API_TOKEN` (também funciona com `UPSTASH_REDIS_REST_URL` / `UPSTASH_REDIS_REST_TOKEN`).
4. **Redeploy:** *Deployments → … → Redeploy*, para o app enxergar o banco.
5. Abra o endereço do app. Na primeira vez aparece **Primeiro acesso**: cadastre o coordenador (nome, usuário e senha).

> Se for usar o mesmo banco do Controle de Equipamentos, não tem problema: todas as chaves deste app começam com `cl:`.

## Primeiros passos do coordenador

1. **Cadastros → Ruas:** ajuste os nomes das 14 ruas (vêm como Rua 01 a Rua 14).
2. **Cadastros → Importar planilha:** baixe o modelo, preencha as abas *Colaboradores* e *Usuarios* (líderes) e envie. Também dá para cadastrar um a um pela tela.
3. **Configurações:** confira itens do check-list, horários e dias de cada turno, e cadastre feriados em *Dias sem operação*.

## Perfis

| Quem | Como entra | O que vê / faz |
|---|---|---|
| Colaborador (dono de rua) | Digita o nome e toca nele na lista | Só o check-list da(s) sua(s) rua(s), no turno em andamento. Preenche e assina. |
| Líder | Usuário e senha | Painel e validação dos turnos dele, define substitutos, relatórios. |
| Visualização | Usuário e senha | Painel, aderência e relatórios, sem assinar nem editar. |
| Coordenador | Usuário e senha | Tudo, incluindo cadastros, configurações e acessos. |

## Regras

- O check-list só pode ser preenchido durante o turno. Ao fim do turno, sem envio, a rua vira **Não realizado**.
- O T3 (22:00–06:00) conta na data de início do turno.
- Itens: Feito, Não feito ou N/A. Se algum item for "Não feito", a observação é obrigatória. N/A conta como item ok na aderência.
- Líder valida o check-list inteiro até o fim do turno seguinte. Depois disso fica **Sem validação** e só o coordenador pode validar (registrado como fora do prazo).
- Reprovação exige motivo e devolve o check-list ao colaborador, que refaz e reenvia. Tudo fica no histórico.
- Validado = travado, sem edição.
- Substituto: o líder escolhe data, turno, rua e colaborador. Vale só naquele turno.
- Assinatura eletrônica: nome, data, hora e aparelho.
- Mudanças nos itens valem dali para frente; check-lists antigos não mudam.
- Histórico guardado por 12 meses.

## Testar no computador

```
node dev-server.js
```
Abra http://localhost:3000. Sem as variáveis do Upstash, os dados ficam no arquivo `.devdb.json` (só para teste).

## Estrutura

- `public/` – telas (HTML/CSS/JS), logo e bibliotecas de PDF/Excel embutidas
- `api/index.js` – API (função da Vercel)
- `lib/regras.js` – regras de negócio; `lib/store.js` – acesso ao Redis
