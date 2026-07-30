# Instalar

## Claude Code

```bash
git clone https://github.com/inovacao-totalcad/Totalcad-ZWCAD.git
cp -r Totalcad-ZWCAD/skills/totalcad-lisp ~/.claude/skills/
```

Para valer só num projeto, troque o destino por `.claude/skills/`.

## Outro agente

Antigravity, Codex, OpenCode, Cursor — peça ao próprio agente:

> *"instale esta skill que está na pasta Totalcad-ZWCAD/skills/totalcad-lisp"*

Ele sabe onde a pasta de skills dele fica.

## Sem git

Baixe em **Code → Download ZIP** no GitHub e copie a pasta `skills/totalcad-lisp` de dentro do ZIP
para a pasta de skills do seu agente. O `SKILL.md` tem que ficar na raiz de `totalcad-lisp/`.

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
