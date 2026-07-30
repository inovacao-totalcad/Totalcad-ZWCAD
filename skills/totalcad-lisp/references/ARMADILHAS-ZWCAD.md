# O que a IA erra no ZWCAD

**Leia antes de escrever código.** Estes são os pontos em que um assistente genérico gera LISP que
*parece* certo e não roda — ou pior, roda e falha calado.

Cada item aqui é fato do ZWCAD, verificado na documentação oficial da ZWSOFT que está em `docs/`.

---

## 1. Proteger o código: comando `COMPILE`

🔴 **Não invente função de compilação.** No ZWCAD, proteger código é operação de interface, pelo
comando **`COMPILE`**:

```
1. Digite COMPILE
2. "Select File"           → escolha o .lsp
3. "Select Save Directory" → escolha onde salvar
4. Formato de saída: .zelx  (ou .lsp criptografado)
5. "Compile"
```

**Formato de distribuição: `.zelx`.** É binário, criptografado, e não se edita.

⚠️ **O `.zelx` é via de mão única.** Se o usuário perder o `.lsp` de origem, a LISP morre — não há
como voltar do `.zelx` para o fonte. Diga isso na entrega e mande ele guardar a pasta `Source/`.

⚠️ **Não existe função LISP que compile.** Se você escrever qualquer chamada de compilação dentro do
código, vai falhar. O `COMPILE` é comando digitado pelo usuário, uma vez, no fim do processo.

---

## 2. Funções que não existem no ZWCAD

Se você usar qualquer uma destas, a LISP quebra:

| Função | O que fazer no lugar |
|---|---|
| ⚠️ `layerstate-getnames` | Use COM: `(vlax-create-object "ZWCAD.ZcadLayerStateManager.<ano>")` |
| `vl-vlx-loaded-p` | Teste a existência da sua função: `(if (null c:MEU_COMANDO) ...)` |
| `vl-list-loaded-vlx` | Sem equivalente. Controle o carregamento por variável global própria |
| `vl-unload-vlx` | Sem equivalente. Não tente descarregar por código |
| `tablet` | Mesa digitalizadora. Legado, ignore |
| ⚠️ `ACET-*` (qualquer uma) | Não existem. Escreva a lógica na mão, ou use função equivalente do ZWCAD |

**`layerstate-getnames` é a pior**, porque "salvar e restaurar estado de layer" é pedido comum e o
erro só aparece na hora de rodar.

**`ACET-*` é a mais frequente** em código de terceiro. Se o usuário trouxer um LISP de fora que
acusa `undefined function - ACET-...`, essa é a causa.

---

## 3. Identificadores COM: erra o nome e recebe `nil` **sem aviso**

🔴 **A falha mais traiçoeira.** `vlax-create-object` com identificador de outra plataforma **não dá
erro** — devolve `nil`, e a LISP estoura depois, num ponto que não tem relação com a causa.

Os identificadores do ZWCAD:

| Objeto | Identificador |
|---|---|
| Aplicação | `ZWCAD.Application.<ano>` |
| Desenho | `ZWCAD.Drawing.<ano>` |
| Gerenciador de estado de layer | `ZWCAD.ZcadLayerStateManager.<ano>` |
| Cor | `ZWCAD.ZcCmColor.<ano>` |

```lisp
;; <ano> e a versao anual: 2025, 2026, 2027...
(vlax-create-object "ZWCAD.ZcCmColor.2026")
```

⚠️ **O ano no fim amarra o código a uma versão.** Se a LISP vai rodar em máquinas diferentes,
tente os anos em sequência em vez de fixar um:

```lisp
(defun zw-com (base / obj anos)
  (setq anos '("2027" "2026" "2025"))
  (while (and anos (null obj))
    (setq obj (vl-catch-all-apply 'vlax-create-object
                (list (strcat base "." (car anos)))))
    (if (vl-catch-all-error-p obj) (setq obj nil))
    (setq anos (cdr anos)))
  obj)
```

Nomes de classe internos usam o prefixo **`Zcad`** — `ZcadDocument`, `ZcadModelSpace`, `ZcadLine`.

---

## 4. Carregar módulo binário: `zrxload`

```lisp
(zrxload "modulo.zrx")
```

`.zrx` é o binário de extensão do ZWCAD, compilado com o ZRX SDK.

## 5. Arquivos de recurso do ZWCAD

Se o código referencia arquivo de configuração por nome:

| Recurso | Arquivo |
|---|---|
| Tipos de linha | `ZWCAD.lin` |
| Tipos de linha ISO | `ZWCADiso.lin` |
| Hachuras | `ZWCAD.pat` |
| Hachuras ISO | `ZWCADiso.pat` |
| Customização de interface | `ZWCAD.cuix` |

## 6. Variáveis de sistema não retornam o que você espera

Algumas variáveis do ZWCAD devolvem valores próprios — `ROAMABLEROOTPREFIX`, por exemplo, aponta
para o caminho de instalação do ZWCAD.

⚠️ **Nunca monte caminho de arquivo colando texto no retorno dessas variáveis.** Para gravar arquivo
de apoio use `vl-filename-mktemp`; para guardar configuração use o registro do Windows.

---

## 7. A janela DCL não aparece

Duas causas, nesta ordem:

1. **Faltam `base.dcl` e `primitives.dcl`** no caminho de busca de suporte. Os dois estão na pasta
   de instalação do ZWCAD e precisam estar no *support search path*
2. **Controle não definido** no DCL — confira contra a documentação em `docs/DCL/`

## 8. Comandos nativos: confirme antes de encadear

A ordem e o nome das opções de um comando nativo podem não ser o que você supõe. Se a LISP depende
de `(command "_.ALGUM" opt1 opt2 opt3 ...)` com muitas opções encadeadas:

- **Consulte `docs/LISP_Reference/`** antes de escrever
- **Avise o usuário para testar esse trecho especificamente**
- Prefira a rota ActiveX/COM quando existir equivalente

---

## 9. Tabela de erros do console

| Mensagem | Causa |
|---|---|
| `undefined function - ACET-*` | Função ACET, que não existe no ZWCAD (item 2) |
| `undefined function - nil` | Função não definida — confira o nome, ou o arquivo não carregou inteiro |
| `ZWCAD variable setting rejected` | `setvar` com valor fora de faixa, tipo errado, ou variável inexistente |
| `misplaced right/left paren` | Parêntese sobrando ou faltando |
| `quit / exit abort` | `quit`/`exit` chamado onde não devia — reveja a lógica |
| LISP roda, não reclama e não funciona | Suspeite de `vlax-create-object` devolvendo `nil` (item 3) |

---

## Em dúvida sobre uma função? Consulte, não chute.

**`docs/LISP_Reference/`** tem 476 arquivos, um por função, organizados pela letra inicial:

```
docs/LISP_Reference/s Functions/ssget.html
docs/LISP_Reference/v Functions/vlax-create-object.html
```

Cada arquivo tem ~6 KB. **Ler um é mais barato que errar um.**

---

## Fontes

| Afirmação | Onde está |
|---|---|
| `COMPILE` gera `.lsp` cifrado e `.zelx` | `docs/LISP_Migration/LISP Encryption.md` |
| Formatos de LISP | `docs/LISP_Migration/LISP Plug-in Format.md` |
| `base.dcl` / `primitives.dcl` | `docs/LISP_Migration/FAQ.md`, Q4 |
| `ACET-*` ausente | `docs/LISP_Migration/FAQ.md`, Q5 |
| Identificadores COM e arquivos de recurso | `docs/LISP_Migration/2-1-Differences Introduction.md` |
| `zrxload` | `docs/LISP_Migration/Migration Steps.md` |
| Funções ausentes | `ZWCAD 2027 API Status_LISP.xlsx` (ZWSOFT) |
| Referência por função | `docs/LISP_Reference/` |
