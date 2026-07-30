# Padrão de código — rotina LISP para ZWCAD

Estrutura fixa. Não improvise a arquitetura; troque só o conteúdo entre os marcadores
`;; >>> LÓGICA` e `;; <<< LÓGICA`.

Substitua os placeholders pelos dados de `IDENTIDADE.md`:

| Placeholder | Vem de | Exemplo |
|---|---|---|
| `<PREFIXO>` | `PREFIXO` | `AC` |
| `<MARCA>` | `MARCA` | `Bali.CAD` |
| `<AUTOR>` | `AUTOR` | `Alison Cruz` |
| `<CONTATO>` | `CONTATO` | `fulano@email.com` |
| `<SOCIAL>` | `SOCIAL` | `@fulano` |
| `<NomeRotina>` | da LISP | `ArrumaLayer` |

## Regras de nomenclatura

- Arquivo: `LISP_<NomeRotina>.lsp`
- Comando principal: `<PREFIXO>_<NomeRotina>` — **sem espaço, sem acento**
- Título da janela: `<MARCA> - <Nome legível>` — **nunca com número de versão**
- Versão: SemVer, começa em `1.0.0`

---

## Esqueleto

```lisp
;;; ==========================================================================
;;;  LISP: <MARCA> - <Nome legível>
;;;  AUTOR: <AUTOR>
;;;  VERSAO: 1.0.0
;;;  DESCRICAO: <uma linha sobre o que faz>
;;; ==========================================================================

(vl-load-com)

;;; --- IDENTIFICACAO -------------------------------------------------------
(setq *rot-nome*  "<Nome legível>")
(setq *rot-cmd*   "<PREFIXO>_<NomeRotina>")   ;; comando, sem espaco
(setq *rot-ver*   "1.0.0")
(setq *rot-autor* "<AUTOR>")
(setq *rot-cont*  "<CONTATO>")
(setq *rot-socl*  "<SOCIAL>")
(setq *rot-marca* "<MARCA>")
(setq *rot-reg*   "HKEY_CURRENT_USER\\Software\\<MARCA>\\<NomeRotina>")

;;; --- ALERT DE CARREGAMENTO ----------------------------------------------
(defun rot-startup ()
  (alert
    (strcat
      "LISP CARREGADO\n\n"
      "Nome: "    *rot-marca* " - " *rot-nome* "\n"
      "Versao: "  *rot-ver*   "\n"
      "Autor: "   *rot-autor* "\n\n"
      "DIGITE PARA INICIAR:  " *rot-cmd*
    )
  )
  (princ (strcat "\n" *rot-marca* ": " *rot-nome* " v" *rot-ver*
                 " carregado. Digite " *rot-cmd* " para iniciar."))
  (princ)
)
(rot-startup)

;;; --- MODO DEBUG ----------------------------------------------------------
;;; Enquanto T, a rotina narra o que faz e grava LOG_<COMANDO>.txt.
;;; VERSAO DE TESTE: T   |   VERSAO FINAL: nil
(setq *rot-debug* T)

;;; Log fica junto do desenho. Se o desenho nunca foi salvo, cai no temporario.
(defun rot-log-arq ( / pre)
  (setq pre (getvar "DWGPREFIX"))
  (if (or (null pre) (= pre ""))
    (vl-filename-mktemp (strcat "LOG_" *rot-cmd* ".txt"))
    (strcat pre "LOG_" *rot-cmd* ".txt")
  )
)

(defun rot-log (txt / f)
  (if *rot-debug*
    (progn
      (princ (strcat "\n  [dbg] " txt))
      (if (setq f (open (rot-log-arq) "a"))
        (progn
          (write-line (strcat (rtos (getvar "CDATE") 2 6) "  " txt) f)
          (close f)
        )
      )
    )
  )
  (princ)
)

;;; Cabecalho do log, uma vez por execucao
(defun rot-log-inicio ()
  (if *rot-debug*
    (progn
      (rot-log "===================================================")
      (rot-log (strcat "INICIO  " *rot-nome* " v" *rot-ver*))
      (rot-log (strcat "desenho: " (getvar "DWGNAME")))
      (rot-log (strcat "unidade INSUNITS: " (itoa (getvar "INSUNITS"))))
      (princ (strcat "\n  [dbg] versao de TESTE - log em " (rot-log-arq)))
    )
  )
  (princ)
)

;;; --- TRATAMENTO DE ERRO --------------------------------------------------
;;; Guarda o que mexeu e devolve no lugar, inclusive quando o usuario da ESC.
(setq *rot-cmdecho* nil)

(defun *error* (msg)
  (if (not (wcmatch (strcase msg t) "*break*,*cancel*,*exit*"))
    (progn
      (princ (strcat "\nErro em " *rot-nome* ": " msg))
      (rot-log (strcat "*** ERRO: " msg))          ;; vai para o log tambem
    )
    (progn
      (princ "\nCancelado pelo usuario.")
      (rot-log "cancelado pelo usuario (ESC)")
    )
  )
  (if *rot-cmdecho* (setvar "CMDECHO" *rot-cmdecho*))
  (rot-undo-fim)
  (if *rot-debug* (princ (strcat "\n  [dbg] log em " (rot-log-arq))))
  (princ)
)

;;; --- UNDO EM UM PASSO ----------------------------------------------------
;;; Sem isto o usuario precisa apertar Ctrl+Z dez vezes para desfazer.
(defun rot-undo-ini () (command "_.UNDO" "_Begin") (princ))
(defun rot-undo-fim ()
  (if (= 0 (getvar "CMDACTIVE")) (command "_.UNDO" "_End"))
  (princ)
)

;;; --- PERSISTENCIA --------------------------------------------------------
(defun rot-salva (chave valor)
  (vl-registry-write *rot-reg* chave valor)
)
(defun rot-le (chave default / v)
  (setq v (vl-registry-read *rot-reg* chave))
  (if (or (null v) (= v "")) default v)
)

;;; --- SOBRE E AJUDA -------------------------------------------------------
(defun rot-sobre ()
  (alert
    (strcat
      *rot-marca* " - " *rot-nome* "\n"
      "Versao: " *rot-ver* "\n\n"
      "Autor: "  *rot-autor* "\n"
      "Contato: " *rot-cont* "\n"
      (if (and *rot-socl* (/= *rot-socl* "")) (strcat *rot-socl* "\n") "")
      "\nTodos os direitos reservados."
    )
  )
)

;;; A ajuda vive DENTRO da LISP. Sem arquivo externo, sem site, sem internet.
;;; Escreva o texto na hora de gerar o codigo, a partir da entrevista.
(defun rot-ajuda ()
  (alert
    (strcat
      "COMO USAR — " *rot-nome* "\n\n"
      "1. Digite " *rot-cmd* "\n"
      "2. <o que o usuario faz — vem da pergunta 2 da entrevista>\n"
      "3. <o que acontece — vem da pergunta 4>\n\n"
      "ATALHO DIRETO (sem a janela):\n"
      "   " *rot-cmd* "_QUICK\n\n"
      "OBSERVACOES:\n"
      "   - <limitacao que apareceu na entrevista>\n"
      "   - Ctrl+Z desfaz tudo de uma vez"
    )
  )
)

;;; --- INTERFACE DCL (TEMPORARIA) -----------------------------------------
;;; PROIBIDO entregar .dcl solto. Gera, usa, apaga.
(defun rot-escreve-dcl (arq / f)
  (setq f (open arq "w"))
  (write-line
    (strcat
      "rot_main : dialog {"
      "  label = \"" *rot-marca* " - " *rot-nome* "\";"
      "  : boxed_column { label = \"Opcoes\";"
      ;; >>> CONTROLES — trocar conforme a entrevista
      "     : edit_box { label = \"Valor:\"; key = \"in_valor\"; edit_width = 10; }"
      "     : toggle   { label = \"Incluir layers congelados\"; key = \"tg_congelado\"; }"
      ;; <<< CONTROLES
      "  }"
      "  : text { label = \"\"; key = \"msg_erro\"; }"
      "  : spacer { height = 1; }"
      "  : row { fixed_width = true; alignment = centered;"
      "    : button { label = \"Executar\"; key = \"accept\"; is_default = true; width = 12; }"
      "    : spacer { width = 1; }"
      "    : button { label = \"Ajuda\";    key = \"btn_ajuda\"; width = 10; }"
      "    : spacer { width = 1; }"
      "    : button { label = \"Sobre\";    key = \"btn_sobre\"; width = 10; }"
      "    : spacer { width = 1; }"
      "    : button { label = \"Fechar\";   key = \"cancel\"; is_cancel = true; width = 10; }"
      "  }"
      "}"
    )
    f
  )
  (close f)
)

;;; --- COMANDO DIRETO (para quem nao quer a janela) -----------------------
;;; Acesso duplo: tudo que a janela faz tem que ter comando proprio.
(defun c:<PREFIXO>_<NomeRotina>_QUICK ()
  (rot-executa (rot-le "Valor" "10.0") (= "1" (rot-le "Congelado" "0")))
)

;;; --- LOGICA --------------------------------------------------------------
(defun rot-executa (valor incluir-congelado / ss i ent)
  (rot-log-inicio)
  (rot-log (strcat "parametros: valor=" (rtos valor 2 4)
                   " congelado=" (if incluir-congelado "sim" "nao")))
  (setq *rot-cmdecho* (getvar "CMDECHO"))
  (setvar "CMDECHO" 0)
  (rot-undo-ini)

  ;; >>> LOGICA
  (setq ss (ssget))                    ;; nil se o usuario nao selecionar nada
  (if (null ss)
    (progn
      (rot-log "selecao vazia - nada a fazer")
      (princ "\nNada selecionado. Nenhuma alteracao feita.")
    )
    (progn
      (rot-log (strcat "selecionados: " (itoa (sslength ss)) " objeto(s)"))
      (setq i 0)
      (while (< i (sslength ss))
        (setq ent (ssname ss i))
        ;; ... trabalho aqui ...
        ;; REGISTRE CADA DECISAO. E aqui que se descobre por que deu errado:
        (rot-log (strcat "  [" (itoa (1+ i)) "] "
                         (cdr (assoc 0 (entget ent)))   ;; tipo do objeto
                         " layer=" (cdr (assoc 8 (entget ent)))))
        (setq i (1+ i))
      )
      (rot-log (strcat "CONCLUIDO: " (itoa (sslength ss)) " objeto(s) processado(s)"))
      (princ (strcat "\nConcluido: " (itoa (sslength ss)) " objeto(s)."))
    )
  )
  ;; <<< LOGICA

  (rot-undo-fim)
  (setvar "CMDECHO" *rot-cmdecho*)
  (if *rot-debug*
    (princ (strcat "\n  [dbg] log salvo em " (rot-log-arq)
                   "\n  [dbg] deu errado? mande este arquivo inteiro")))
  (princ)
)

;;; --- COMANDO PRINCIPAL ---------------------------------------------------
(defun c:<PREFIXO>_<NomeRotina> ( / dcl_arq dcl_id res valor congelado)
  (setq dcl_arq (vl-filename-mktemp "rot_gui.dcl"))
  (rot-escreve-dcl dcl_arq)
  (setq dcl_id (load_dialog dcl_arq))

  (if (< dcl_id 0)
    (progn
      (alert "Nao foi possivel carregar a interface.")
      (vl-file-delete dcl_arq)
      (princ)
    )
    (if (not (new_dialog "rot_main" dcl_id))
      (progn
        (alert (strcat "Nao foi possivel abrir a janela.\n\n"
                       "Verifique se base.dcl e primitives.dcl estao\n"
                       "no caminho de suporte do ZWCAD."))
        (unload_dialog dcl_id)
        (vl-file-delete dcl_arq)
        (princ)
      )
      (progn
        ;; estado inicial vem do que ficou salvo da ultima vez
        (set_tile "in_valor"     (rot-le "Valor" "10.0"))
        (set_tile "tg_congelado" (rot-le "Congelado" "0"))

        ;; validacao ao vivo: nao deixe o usuario descobrir o erro no OK
        (action_tile "in_valor"
          "(if (<= (atof $value) 0)
             (progn (set_tile \"msg_erro\" \"Valor deve ser maior que zero.\")
                    (mode_tile \"accept\" 1))
             (progn (set_tile \"msg_erro\" \"\")
                    (mode_tile \"accept\" 0)))")

        (action_tile "btn_ajuda" "(rot-ajuda)")
        (action_tile "btn_sobre" "(rot-sobre)")
        (action_tile "accept"
          "(progn (setq valor (get_tile \"in_valor\")
                       congelado (get_tile \"tg_congelado\"))
                  (done_dialog 1))")
        (action_tile "cancel" "(done_dialog 0)")

        (setq res (start_dialog))
        (unload_dialog dcl_id)
        (vl-file-delete dcl_arq)          ;; nao deixar lixo na maquina

        (if (= res 1)
          (progn
            (rot-salva "Valor" valor)
            (rot-salva "Congelado" congelado)
            (rot-executa (atof valor) (= congelado "1"))
          )
          (princ "\nCancelado.")
        )
      )
    )
  )
  (princ)
)

(princ)
;;; FIM
```

---

## O que mudou em relação ao padrão antigo, e por quê

Se você conhece a versão anterior deste template, estes pontos foram corrigidos:

| Correção | Motivo |
|---|---|
| Comando vem de `*rot-cmd*`, não de `(strcase *rot-nome*)` | Nome com espaço gerava comando inválido: `"Count Plus"` → `AC_COUNT PLUS` |
| Linha `"Autor: "` aparecia duas vezes no alert | Erro de cópia |
| `[Ajuda]` mostra o texto de uso **dentro do próprio arquivo** | Antes abria um site. Uma rotina de um arquivo só não deve depender de internet nem de arquivo vizinho para explicar a si mesma |
| ⭐ **Modo debug** com `rot-log` gravando TXT | Sem log, o relato que volta é *"não funcionou"* — e a correção vira chute |
| `*error*` presente, restaurando `CMDECHO` e fechando o UNDO | Sem isso, um ESC no meio deixa o desenho com `CMDECHO` desligado e o grupo de undo aberto |
| `rot-undo-ini` / `rot-undo-fim` | Sem isso o usuário aperta Ctrl+Z dez vezes para desfazer uma execução |
| `load_dialog` testa retorno negativo | `(exit)` dentro de `if` abortava sujo e deixava o `.dcl` temporário no disco |
| Erro de janela cita `base.dcl`/`primitives.dcl` | É a causa nº 1 de DCL que não abre no ZWCAD |
| `ssget` sem seleção é tratado | `(sslength nil)` derruba a LISP |

## Testes obrigatórios antes de entregar

Peça ao usuário para rodar os quatro:

1. **Caminho normal** — funciona e o resultado é o que ele espera
2. **ESC no meio** — não deixa mensagem feia nem trava o desenho
3. **Nada selecionado** — avisa e sai sem alterar
4. **Ctrl+Z uma vez** — desfaz tudo

Se qualquer um falhar, **peça o TXT do log** e corrija. Nova versão, `PATCH` acima, debug ainda
ligado.

---

## Virar a versão final

**Só depois do usuário confirmar em palavras que está do jeito que ele precisa.**

Uma linha muda:

```lisp
(setq *rot-debug* nil)        ;; era T
```

E a versão sobe para `1.0.0`.

⚠️ **Não apague as chamadas `rot-log`.** Com o debug desligado elas ficam inertes — não gravam, não
imprimem, não custam nada. E se a rotina der problema daqui a seis meses, o usuário volta o `T` e
tem o log de novo. Apagar o debug é jogar fora a única ferramenta de diagnóstico que ele tem.

Diga isso a ele com estas palavras:

> *"Se algum dia der problema, abra o `.lsp`, troque `(setq *rot-debug* nil)` por
> `(setq *rot-debug* T)` e rode de novo — a rotina volta a gravar o log."*
