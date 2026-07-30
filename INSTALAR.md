# Instalar

## Claude Code

```bash
git clone https://github.com/inovacao-totalcad/Totalcad-ZWCAD.git ~/.claude/skills/totalcad-lisp
```

Para valer só num projeto, troque o destino por `.claude/skills/totalcad-lisp`.

## Outro agente

Antigravity, Codex, OpenCode, Cursor — peça ao próprio agente:

> *"instale esta skill que está na pasta Totalcad-ZWCAD"*

Ele sabe onde a pasta de skills dele fica.

## Sem git

Baixe em **Code → Download ZIP** no GitHub e descompacte dentro da pasta de skills do seu agente.
O `SKILL.md` precisa ficar na **raiz** da pasta que você criar.

---

## Testar se pegou

Pergunte ao seu agente:

> **como eu protejo uma LISP no ZWCAD?**

| Resposta | Significa |
|---|---|
| Cita o comando **`COMPILE`** e o formato **`.zelx`** | ✅ Instalada |
| Inventa uma função de compilação, ou não sabe | ❌ Não carregou |

## Primeiro uso

Diga o que te dá trabalho, em linguagem normal, sem tentar descrever a solução:

> *"perco 40 minutos arrumando os layers de todo arquivo que recebo do cliente"*

A skill vai perguntar como você faz isso hoje, na mão, e construir a partir daí.
