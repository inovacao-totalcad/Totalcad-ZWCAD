# Padrão de código — plugin LISP para ZWCAD

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
| `<NomePlugin>` | do plugin | `ArrumaLayer` |

## Regras de nomenclatura

- Arquivo: `LISP_<NomePlugin>.lsp`
- Comando principal: `<PREFIXO>_<NomePlugin>` — **sem espaço, sem acento**
- Título da janela: `<MARCA> - <Nome legível>` — **nunca com número de versão**
- Versão: SemVer, começa em `1.0.0`

---

## Esqueleto

```lisp
;;; ==========================================================================
;;;  PLUGIN: <MARCA> - <Nome legível>
;;;  AUTOR: <AUTOR>
;;;  VERSAO: 1.0.0
;;;  DESCRICAO: <uma linha sobre o que faz>
;;; ==========================================================================

(vl-load-com)

;;; --- IDENTIFICACAO -------------------------------------------------------
(setq *plg-nome*  "<Nome legível>")
(setq *plg-cmd*   "<PREFIXO>_<NomePlugin>")   ;; comando, sem espaco
(setq *plg-ver*   "1.0.0")
(setq *plg-autor* "<AUTOR>")
(setq *plg-cont*  "<CONTATO>")
(setq *plg-socl*  "<SOCIAL>")
(setq *plg-marca* "<MARCA>")
(setq *plg-reg*   "HKEY_CURRENT_USER\\Software\\<MARCA>\\<NomePlugin>")
(setq *plg-help*  nil)   ;; caminho do manual .html, definido no passo de instalacao

;;; --- ALERT DE CARREGAMENTO ----------------------------------------------
(defun plg-startup ()
  (alert
    (strcat
      "PLUGIN CARREGADO\n\n"
      "Nome: "    *plg-marca* " - " *plg-nome* "\n"
      "Versao: "  *plg-ver*   "\n"
      "Autor: "   *plg-autor* "\n\n"
      "DIGITE PARA INICIAR:  " *plg-cmd*
    )
  )
  (princ (strcat "\n" *plg-marca* ": " *plg-nome* " v" *plg-ver*
                 " carregado. Digite " *plg-cmd* " para iniciar."))
  (princ)
)
(plg-startup)

;;; --- TRATAMENTO DE ERRO --------------------------------------------------
;;; Guarda o que mexeu e devolve no lugar, inclusive quando o usuario da ESC.
(setq *plg-cmdecho* nil)

(defun *error* (msg)
  (if (not (wcmatch (strcase msg t) "*break*,*cancel*,*exit*"))
    (princ (strcat "\nErro em " *plg-nome* ": " msg))
    (princ "\nCancelado pelo usuario.")
  )
  (if *plg-cmdecho* (setvar "CMDECHO" *plg-cmdecho*))
  (plg-undo-fim)
  (princ)
)

;;; --- UNDO EM UM PASSO ----------------------------------------------------
;;; Sem isto o usuario precisa apertar Ctrl+Z dez vezes para desfazer.
(defun plg-undo-ini () (command "_.UNDO" "_Begin") (princ))
(defun plg-undo-fim ()
  (if (= 0 (getvar "CMDACTIVE")) (command "_.UNDO" "_End"))
  (princ)
)

;;; --- PERSISTENCIA --------------------------------------------------------
(defun plg-salva (chave valor)
  (vl-registry-write *plg-reg* chave valor)
)
(defun plg-le (chave default / v)
  (setq v (vl-registry-read *plg-reg* chave))
  (if (or (null v) (= v "")) default v)
)

;;; --- SOBRE E AJUDA -------------------------------------------------------
(defun plg-sobre ()
  (alert
    (strcat
      *plg-marca* " - " *plg-nome* "\n"
      "Versao: " *plg-ver* "\n\n"
      "Autor: "  *plg-autor* "\n"
      "Contato: " *plg-cont* "\n"
      (if (and *plg-socl* (/= *plg-socl* "")) (strcat *plg-socl* "\n") "")
      "\nTodos os direitos reservados."
    )
  )
)

;;; A ajuda vive DENTRO do plugin. Sem arquivo externo, sem site, sem internet.
;;; Escreva o texto na hora de gerar o codigo, a partir da entrevista.
(defun plg-ajuda ()
  (alert
    (strcat
      "COMO USAR — " *plg-nome* "\n\n"
      "1. Digite " *plg-cmd* "\n"
      "2. <o que o usuario faz — vem da pergunta 2 da entrevista>\n"
      "3. <o que acontece — vem da pergunta 4>\n\n"
      "ATALHO DIRETO (sem a janela):\n"
      "   " *plg-cmd* "_QUICK\n\n"
      "OBSERVACOES:\n"
      "   - <limitacao que apareceu na entrevista>\n"
      "   - Ctrl+Z desfaz tudo de uma vez"
    )
  )
)

;;; --- INTERFACE DCL (TEMPORARIA) -----------------------------------------
;;; PROIBIDO entregar .dcl solto. Gera, usa, apaga.
(defun plg-escreve-dcl (arq / f)
  (setq f (open arq "w"))
  (write-line
    (strcat
      "plg_main : dialog {"
      "  label = \"" *plg-marca* " - " *plg-nome* "\";"
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
(defun c:<PREFIXO>_<NomePlugin>_QUICK ()
  (plg-executa (plg-le "Valor" "10.0") (= "1" (plg-le "Congelado" "0")))
)

;;; --- LOGICA --------------------------------------------------------------
(defun plg-executa (valor incluir-congelado / ss)
  (setq *plg-cmdecho* (getvar "CMDECHO"))
  (setvar "CMDECHO" 0)
  (plg-undo-ini)

  ;; >>> LOGICA
  (setq ss (ssget))                    ;; nil se o usuario nao selecionar nada
  (if (null ss)
    (princ "\nNada selecionado. Nenhuma alteracao feita.")
    (progn
      ;; ... trabalho aqui ...
      (princ (strcat "\nConcluido: " (itoa (sslength ss)) " objeto(s)."))
    )
  )
  ;; <<< LOGICA

  (plg-undo-fim)
  (setvar "CMDECHO" *plg-cmdecho*)
  (princ)
)

;;; --- COMANDO PRINCIPAL ---------------------------------------------------
(defun c:<PREFIXO>_<NomePlugin> ( / dcl_arq dcl_id res valor congelado)
  (setq dcl_arq (vl-filename-mktemp "plg_gui.dcl"))
  (plg-escreve-dcl dcl_arq)
  (setq dcl_id (load_dialog dcl_arq))

  (if (< dcl_id 0)
    (progn
      (alert "Nao foi possivel carregar a interface.")
      (vl-file-delete dcl_arq)
      (princ)
    )
    (if (not (new_dialog "plg_main" dcl_id))
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
        (set_tile "in_valor"     (plg-le "Valor" "10.0"))
        (set_tile "tg_congelado" (plg-le "Congelado" "0"))

        ;; validacao ao vivo: nao deixe o usuario descobrir o erro no OK
        (action_tile "in_valor"
          "(if (<= (atof $value) 0)
             (progn (set_tile \"msg_erro\" \"Valor deve ser maior que zero.\")
                    (mode_tile \"accept\" 1))
             (progn (set_tile \"msg_erro\" \"\")
                    (mode_tile \"accept\" 0)))")

        (action_tile "btn_ajuda" "(plg-ajuda)")
        (action_tile "btn_sobre" "(plg-sobre)")
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
            (plg-salva "Valor" valor)
            (plg-salva "Congelado" congelado)
            (plg-executa (atof valor) (= congelado "1"))
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
| Comando vem de `*plg-cmd*`, não de `(strcase *plg-nome*)` | Nome com espaço gerava comando inválido: `"Count Plus"` → `AC_COUNT PLUS` |
| Linha `"Autor: "` aparecia duas vezes no alert | Erro de cópia |
| `[Ajuda]` mostra o texto de uso **dentro do próprio plugin** | Antes abria um site. Um plugin de um arquivo não deve depender de internet nem de arquivo vizinho para explicar a si mesmo |
| `*error*` presente, restaurando `CMDECHO` e fechando o UNDO | Sem isso, um ESC no meio deixa o desenho com `CMDECHO` desligado e o grupo de undo aberto |
| `plg-undo-ini` / `plg-undo-fim` | Sem isso o usuário aperta Ctrl+Z dez vezes para desfazer uma execução |
| `load_dialog` testa retorno negativo | `(exit)` dentro de `if` abortava sujo e deixava o `.dcl` temporário no disco |
| Erro de janela cita `base.dcl`/`primitives.dcl` | É a causa nº 1 de DCL que não abre no ZWCAD |
| `ssget` sem seleção é tratado | `(sslength nil)` derruba o plugin |

## Testes obrigatórios antes de entregar

Peça ao usuário para rodar os quatro:

1. **Caminho normal** — funciona e o resultado é o que ele espera
2. **ESC no meio** — não deixa mensagem feia nem trava o desenho
3. **Nada selecionado** — avisa e sai sem alterar
4. **Ctrl+Z uma vez** — desfaz tudo

Se qualquer um falhar, corrija antes de partir para o `COMPILE`.
