# Já é nativo? — verifique antes de construir

O erro mais caro do desenvolvimento assistido por IA em CAD é **construir uma LISP para algo que o
programa já faz.** O usuário perde a aula, ganha mais um arquivo para manter, e a LISP quebra na
próxima versão do ZWCAD.

**Consulte esta lista antes da entrevista.** Se o pedido estiver aqui, pare e ensine o recurso.

---

## Pedidos que já são nativos

| O usuário pede | Recurso nativo | Desde |
|---|---|---|
| Contar quantos blocos/símbolos existem | **Object Count** (paleta) — conta e **gera tabela** | 2026 |
| Transformar objeto repetido e explodido em bloco | **`BATCHBLOCK`** — em lote | 2026 |
| Achar todas as cópias de um símbolo, em escalas e ângulos diferentes | **`SMARTMATCH`** | 2026 |
| Achar bloco parecido em outro desenho e reaproveitar | **`SIMILARSEARCH`** | 2026 |
| Tabela de coordenadas de pontos, numerada | **Coordinate Extraction** | 2027 |
| Extrair atributo de bloco para tabela ou planilha | **`DATAEXTRACTION`** — multi-arquivo, com template reutilizável e tabela que atualiza | 2027 |
| Somar áreas ou comprimentos numa tabela | **Measurement Table** (tabela de comprimento e de área-comprimento) | 2027 |
| Volume, área total, comprimento total | **`MEASUREGEOM`** | 2027 |
| Plotar várias pranchas de uma vez | **Smart Plot** (reconhece o quadro e o tamanho de papel) | 2025 |
| Juntar vários desenhos numa prancha organizada | **Smart Merge** (com prévia e nº de desenhos por linha) | 2027 |
| Inserir bloco alinhado a um objeto existente | **Smart Block Placement / Snap Align** | 2027 |
| Achar e substituir texto em lote | **Find & Replace** (`Ctrl+F9`) — edita sem fechar o painel | 2025 |
| Selecionar por propriedade (tipo QSELECT melhorado) | **Smart Select** — funciona sobre a seleção do próprio usuário | 2025 |
| Selecionar linhas conectadas que estão explodidas | **Seleção em cadeia** — um clique | 2026 |
| Trocar cor/propriedade de layer de xref em lote | **Reference Display** — com regras de filtro exportáveis | 2027 |
| Substituir todas as fontes que faltam | **Font Substitution** — um clique, e lembra a escolha | 2027 |
| Amarrar geometria com restrição (paramétrico) | **Parametrização** — restrição geométrica e dimensional | 2026 |
| Levar configuração para outra máquina/versão | **Configuration Migration** — com execução silenciosa por linha de comando | 2027 |
| Importar arquivo do Revit | **RVT Import** (Revit 2015–2025) | 2027 |
| Ler sistema de coordenadas de outro CAD | **GeoService** — mais de 900 sistemas | 2026 |
| Mapa de fundo (Bing, WMS/WMTS, XYZ) | **Online Map** | 2026 / 2027 |
| Ferramenta utilitária genérica | **Toolbox** — pacote da própria ZWSOFT, já instalado | 2026 |

⚠️ **Nomes de comando confirmados na documentação:** `SMARTMATCH`, `BATCHBLOCK`, `SIMILARSEARCH`,
`SIMILARSEARCHCLOSE`, `DATAEXTRACTION`, `MEASUREGEOM`, `COMPILE`, `APPLOAD`. Os outros itens estão
pelo **nome do recurso** — se o usuário não achar na interface, peça a versão dele e oriente a
procurar pelo nome do recurso, em vez de chutar comando.

---

## Como conduzir quando o pedido já é nativo

Não diga apenas "já existe". Diga:

1. **Qual recurso resolve** e em que versão apareceu
2. **Como chegar nele** (comando ou onde fica)
3. **Por que uma LISP seria pior aqui:** mais um arquivo para instalar em cada máquina, mais uma
   coisa para manter, e alto risco de quebrar quando o ZWCAD atualizar

Depois pergunte: **"isso resolve o seu caso?"**

- **Resolveu** → ótimo, terminou. Não construa nada.
- **Não resolveu** → pergunte **exatamente o que falta**. Essa resposta é o requisito mais
  importante da LISP, e é o que diferencia a LISP do recurso nativo. Registre no manual.

⚠️ **Atenção à versão do usuário.** Se ele está no ZWCAD 2025 e a solução nativa é de 2027, o
recurso não existe na máquina dele. Aí a LISP **faz sentido** — mas registre no manual que a
versão nova resolve nativamente, para ele saber que pode aposentar a LISP depois.

---

## Caso real, para usar como exemplo

Existia uma LISP vendida comercialmente que gerava tabela de coordenadas UTM a partir de
pontos no desenho — trabalho de meses, com contrato e manual.

O ZWCAD 2027 lançou **Coordinate Extraction**: extrai os pontos de vários objetos com um clique e
numera automaticamente numa tabela.

A LISP não era ruim. Só deixou de ser necessário. **É por isso que este passo existe.**
