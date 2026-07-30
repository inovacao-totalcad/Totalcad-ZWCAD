# Protocolo de entrevista

O usuário não sabe programar. Ele sabe **onde dói**. Sua função é transformar dor em especificação.

## Regras da entrevista

1. **Uma pergunta por vez.** Nunca despeje 8 perguntas numa mensagem — ele responde 2 e ignora 6.
2. **Pergunte só o que não consegue deduzir.** Se ele já disse, não pergunte de novo para confirmar.
3. **Ofereça o default junto da pergunta.** *"Vai rodar na seleção que o usuário fizer, ou no desenho
   todo? (o mais comum é seleção)"* — assim ele responde "o comum" quando não sabe.
4. **Máximo de 8 perguntas.** Se precisar de mais, o pedido é grande demais: proponha dividir em
   dois LISPs e construa o primeiro.
5. **Linguagem de CAD, não de programação.** Diga "seleção", "layer", "bloco", "atributo". Nunca
   diga "array", "loop", "callback", "parsing".

---

## Caminho A — ele trouxe só o problema

Ele descreveu uma dor, não uma solução: *"perco 40 minutos arrumando layer de arquivo que recebo do
cliente"*.

Aqui você tem duas tarefas: **descobrir a solução** e **especificá-la**. Comece assim:

> **Pergunta 0 — o passo a passo manual de hoje**
> *"Me conta como você faz isso hoje, na mão, passo a passo. Inclusive as decisões que você toma no
> meio."*

Essa pergunta vale cinco. É onde aparece a regra de negócio que ele nunca pensaria em contar — o
"layer que começa com A-* eu mando pra ARQ, mas se tiver DEMO no nome eu deixo".

Depois siga com as 8 perguntas abaixo, pulando o que a resposta 0 já respondeu.

## Caminho B — ele trouxe a ideia pronta

*"Quero um botão que renomeie layer trocando o prefixo."* A solução já está definida. Vá direto às
perguntas que sobraram — normalmente **2 a 4**.

---

## As 8 perguntas

Use como lista de verificação. Só pergunte o que está em branco.

### 1. Nome do comando
*"Que nome você quer digitar para chamar? Sugiro `<PREFIXO>_<algo curto>`."*
→ Default: derive do que a LISP faz. Comando curto, sem espaço, sem acento.

### 2. O que o usuário faz ao rodar
*"Quando rodar, ele vai selecionar objetos, clicar um ponto, digitar um valor — ou não faz nada e o
LISP trabalha sozinho?"*
→ Default: seleção de objetos. É o caso mais comum.

### 3. Onde a LISP age
*"Só na seleção? No desenho aberto todo? Em todos os layouts? Em vários arquivos de uma pasta?"*
→ Default: na seleção.
→ ⚠️ Se ele disser **vários arquivos**, avise: LISP faz, mas fica lento e frágil. Pergunte quantos
arquivos são. Acima de ~50, registre como limitação no manual.

### 4. ⚠️ Como ele sabe que deu certo
**Esta é a única pergunta sem default. Não invente a resposta.**
*"Depois de rodar, o que você vai olhar na tela para saber que funcionou?"*
→ Se ele não souber responder, o pedido não está maduro. Ajude com exemplos concretos:
*"os 40 layers viraram 6?"*, *"apareceu uma tabela?"*, *"o texto ficou todo no mesmo tamanho?"*

### 5. Interface
*"Precisa de uma janelinha com opções, ou pode ser só perguntas na linha de comando?"*
→ Default: janela DCL se houver **3 ou mais** escolhas para o usuário fazer; linha de comando se
for menos.

### 6. Memória
*"A LISP deve lembrar a última configuração que você usou?"*
→ Default: sim, se houver campo de valor. Salve no registro.

### 7. Reversível
*"Se você rodar e não gostar, precisa desfazer tudo com um Ctrl+Z só?"*
→ Default: sim, sempre que a LISP altera desenho. Agrupe em um undo único.

### 8. O que fazer quando der errado
*"Se ele selecionar o objeto errado, ou nada, o que deve acontecer?"*
→ Default: avisar em linguagem clara e sair sem alterar nada.

---

## Perguntas extras, só quando o caso pedir

| Situação | Pergunte |
|---|---|
| Envolve texto | Precisa diferenciar maiúscula de minúscula? E acento? |
| Envolve bloco | Bloco dinâmico ou atributo entram na conta? |
| Envolve layer | Layer congelado e desligado conta? E os de xref? |
| Envolve tabela | Onde a tabela é inserida — você clica o ponto, ou é automático? |
| Envolve número | Quantas casas decimais? Arredonda para cima ou normal? |
| Envolve unidade | O desenho está em metro, centímetro ou milímetro? |

⚠️ A pergunta de **unidade** é a que mais volta como bug. Se houver qualquer medida envolvida,
pergunte.

---

## Fechamento

Termine sempre com a tabela de premissas e o pedido de autorização:

```
INFORMADO POR VOCÊ
- comando: XX_ArrumaLayer
- age em: seleção de objetos
- resultado: layers com prefixo A- passam para ARQ-
- deu certo quando: a lista de layers cai de ~40 para 6

ASSUMIDO POR MIM — CONFIRME
- layer congelado também é renomeado
- layer de xref é ignorado
- desfaz com um Ctrl+Z único
- lembra a última configuração usada

Posso construir?
```

**Espere o "sim".** Não comece a escrever junto com a pergunta.

Se a lista de assumidos tiver mais itens que a de informados, a entrevista foi curta. Volte e
pergunte mais.
