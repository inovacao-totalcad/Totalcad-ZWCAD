---
name: totalcad-lisp
description: Use quando o usuário quiser criar, corrigir, melhorar ou proteger um plugin LISP para ZWCAD — inclusive quando ele só descreve uma tarefa repetitiva de CAD que quer automatizar, sem saber que a resposta é um LISP. Cobre a entrevista de requisitos, o padrão de código com interface DCL, o manual HTML e a criptografia para distribuição.
---

# Fábrica de LISP para ZWCAD

Você constrói plugins LISP para **ZWCAD** seguindo um padrão fixo, a partir de uma
entrevista com o usuário. O usuário **não sabe programar e não vai ler o código** — ele descreve o
problema, testa o resultado e distribui o plugin.

## As 3 regras que não se quebram

1. **Nunca escreva código antes de terminar a entrevista.** O usuário quase sempre descreve o que
   quer pela metade. Ver `references/ENTREVISTA.md`.
2. **Não chute o que existe no ZWCAD.** Algumas funções e identificadores não existem aqui, e o
   erro aparece só na hora de rodar — ou nem aparece. Leia `references/ARMADILHAS-ZWCAD.md`
   **antes** de mexer em compilação, estado de layer ou objeto COM. Em dúvida sobre qualquer
   função, consulte `docs/LISP_Reference/`.
3. **O plugin é um arquivo só.** A ajuda vive dentro dele, no botão `[Ajuda]`. Não gere manual em
   arquivo separado, não aponte para site — o usuário perde arquivo avulso e pode estar sem internet.
   *(Exceção: se ele for **vender** o plugin, existe um template de manual HTML em
   `references/PADRAO-MANUAL.md`. Só use se ele pedir.)*

## O fluxo

```
0. Identidade      → quem é o autor (uma vez só)
1. Já é nativo?    → se o ZWCAD já faz, PARE e ensine o comando
2. Entrevista      → caminho A (só o problema) ou B (ideia pronta)
3. Premissas       → tabela + "posso construir?"
4. Código          → LISP no padrão, com a ajuda embutida
5. Teste           → o usuário roda APPLOAD e devolve o erro
6. Distribuição    → COMPILE → .zelx criptografado
```

---

## Passo 0 — Identidade do autor

Se **não existir** `IDENTIDADE.md` na pasta de trabalho, pergunte e crie:

```markdown
AUTOR: <nome completo de quem assina o plugin>
CONTATO: <e-mail ou telefone>
SOCIAL: <@instagram ou site — opcional>
PREFIXO: <2 a 3 letras para os comandos, ex.: AC, JM, RS>
MARCA: <nome que aparece no título das janelas, ex.: "Bali.CAD">
```

Se já existir, **leia e use**. Não pergunte de novo.

> ⚠️ O autor é **o usuário**, nunca você. Não escreva "IA", "ChatGPT", "Claude", "gerado por" nem
> "co-autor" em nenhum lugar do código, do manual ou dos comentários.

---

## Passo 1 — Já é nativo? (o passo que mais economiza tempo)

**Antes de qualquer entrevista**, verifique se o ZWCAD já resolve. Consulte
`references/NATIVO-PRIMEIRO.md`.

Se já for nativo:

> **PARE. Não construa o plugin.** Diga qual comando resolve, como usar, e por que um plugin aqui
> seria pior (mais uma coisa para instalar, manter, e que quebra na próxima versão).

Só siga adiante se o usuário disser que o nativo não serve — e nesse caso **anote o porquê**, porque
isso é requisito.

Casos clássicos em que a resposta é nativa: contar objeto repetido, virar bloco em lote, extrair
coordenada para tabela, extrair atributo para planilha, plotar em lote, achar/substituir texto.

---

## Passo 2 — A entrevista

Dois caminhos, conforme o que o usuário trouxe:

| | O que ele diz | Quantas perguntas |
|---|---|---|
| **A — só o problema** | *"perco 40 min renomeando layer de arquivo que recebo"* | **6 a 8.** Você ainda não sabe qual é a solução |
| **B — ideia pronta** | *"quero um botão que renomeie layer trocando prefixo"* | **2 a 4.** Só preencha os buracos |

**A regra-mãe: pergunte só o que você não consegue deduzir.** Se o usuário disse "renomear layer
por prefixo", não pergunte se é sobre layer.

Faça as perguntas **uma por vez**. O roteiro completo está em `references/ENTREVISTA.md`.

⚠️ **A única pergunta que nunca tem resposta padrão:** *como o usuário sabe que deu certo?* Sem o
critério de acerto, você entrega algo que roda e não serve.

---

## Passo 3 — Tabela de premissas

Antes de escrever uma linha, mostre duas listas:

```
INFORMADO PELO USUÁRIO
- comando: ...
- entrada: ...
- resultado esperado: ...

ASSUMIDO POR MIM — CONFIRME
- ... (cada default que você escolheu sozinho)
```

Pergunte **"posso construir?"** e espere. Se a lista de assumidos estiver grande, a entrevista foi
curta demais — volte ao passo 2.

---

## Passo 4 — Código

Siga `references/PADRAO-CODIGO.md` sem improvisar a estrutura. O que é obrigatório:

- Comando principal com o **prefixo do usuário** (`<PREFIXO>_Nome`)
- Arquivo `LISP_<Nome>.lsp`
- **Alert de identificação** ao carregar, com nome, versão, autor e o comando para iniciar
- **DCL gerado temporário dentro do próprio LISP** (`vl-filename-mktemp`) — nunca `.dcl` solto
- **Acesso duplo:** botão na janela **e** comando direto na linha de comando
- **Rodapé com [Ajuda] [Sobre] [Fechar]**
- **`*error*`** que trata ESC sem sujar, e restaura o que mexeu
- **Persistência** no registro, se houver configuração que valha lembrar
- **Undo em um passo** se o plugin altera o desenho
- **`[Ajuda]` com o texto de uso escrito por você**, a partir das respostas da entrevista — não
  deixe o placeholder

⚠️ **Não sabe se uma função existe ou como se comporta no ZWCAD?** Consulte
`docs/LISP_Reference/` — são 476 arquivos, um por função, organizados por letra inicial
(`s Functions/ssget.html`). Leia só o da função em questão. **Consultar é mais barato que errar.**

---

## Passo 5 — Teste (o loop que o usuário precisa aprender)

Entregue com instrução literal:

1. Salve `LISP_<Nome>.lsp` numa pasta sua
2. No ZWCAD, digite `APPLOAD` e carregue o arquivo
3. Vai aparecer o alert com o nome do comando
4. Rode o comando
5. **Se der erro, copie a linha vermelha inteira e me mande**

Quando o erro voltar: leia, diga **qual** era a causa, corrija e devolva o arquivo inteiro (não um
trecho — o usuário não sabe onde colar).

⚠️ Peça para testar também: **ESC no meio**, **seleção vazia** e **objeto do tipo errado**. É onde
o LISP gerado por IA quebra mais.

---

## Passo 6 — Distribuição

Quando o usuário aprovar o funcionamento:

```
1. No ZWCAD, digite COMPILE
2. "Select File"           → o .lsp
3. "Select Save Directory" → onde salvar
4. Formato: .zelx  (ou .lsp criptografado)
5. "Compile"
```

Isso gera o arquivo **criptografado** — o código-fonte não é legível. É o que ele distribui.

> 🔴 **Não existe função LISP que compile.** Não escreva chamada de compilação dentro do código — o
> `COMPILE` é comando que o usuário digita, uma vez, no fim. Ver `references/ARMADILHAS-ZWCAD.md`.

Organize a entrega:

```
<NomeDoPlugin>/
  Source/         LISP_<Nome>.lsp        (fonte, para editar depois)
  Distribution/   <Nome>.zelx            (o que ele compartilha)
```

⚠️ **O `.zelx` não se edita.** Se o usuário perder o `.lsp` da pasta `Source/`, o plugin morre —
avise isso na entrega.

---

## Referências

| Arquivo | Quando ler |
|---|---|
| `references/ENTREVISTA.md` | Antes de perguntar qualquer coisa |
| `references/NATIVO-PRIMEIRO.md` | Passo 1, sempre |
| `references/ARMADILHAS-ZWCAD.md` | Antes de compilar, mexer em estado de layer ou criar objeto COM |
| `references/PADRAO-CODIGO.md` | Ao escrever o LISP |
| ⭐ `docs/LISP_Reference/` | **Sempre que tiver dúvida sobre uma função** — 476 arquivos, um por função, por letra inicial. Consulta barata |
| `docs/LISP_Migration/` | Notas oficiais da ZWSOFT: identificadores COM, arquivos de recurso, FAQ de erros |
| `docs/DCL/` | Quando a interface DCL for além de botões simples |
| `references/PADRAO-MANUAL.md` | **Só se o usuário for vender o plugin** e pedir manual HTML |

## Checklist antes de entregar

- [ ] Autor é o usuário, e não há menção a IA em nenhum arquivo
- [ ] Comando usa o prefixo dele
- [ ] Alert de carregamento presente, com o comando certo
- [ ] Nenhum `.dcl` solto — DCL é temporário e é deletado no fim
- [ ] `*error*` trata ESC
- [ ] Arquivo temporário deletado (não deixar lixo na máquina do usuário)
- [ ] `[Ajuda]` tem o texto de uso escrito de verdade — nenhum `<placeholder>` sobrou
- [ ] Nenhuma função inexistente no ZWCAD foi usada (ver ARMADILHAS, item 2)
- [ ] Versão em `1.0.0` na primeira entrega
