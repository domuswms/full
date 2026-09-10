# Separador Full — site (GitHub Pages)

Página única, sem backend, sem chave de API e sem Apps Script. Lê a planilha
pública do full direto do navegador, mostra os envios e gera as listas de
separação em ISO-8859-1 para subir ao WMS.

## Publicar

1. No repositório do GitHub Pages (`domuswms.github.io`), crie a pasta `full/`.
2. **Add file → Upload files** → arraste o `index.html` → **Commit changes**.
3. Fica no ar em `https://domuswms.github.io/full/`. Atualizar depois é subir o
   arquivo novo por cima (`Ctrl+F5` se o cache insistir).

## Requisito único

A planilha precisa continuar **"qualquer pessoa com o link — leitor"**:

```
1oTrpuueox03WivYXC02J_V7AqEOZpRpLvc12snnzV9c
PREPARAÇÃO FULL PR /SC 2
```

E o **CRONOGRAMA deve permanecer como primeira aba** — não é capricho, veja
"a armadilha" abaixo.

## Como os envios são encontrados

Não existe endpoint que liste as abas de uma planilha pública sem chave de API.
A descoberta é por **tentativa de nome**, data a data, nas duas grafias que a
operação usa:

```
13/12 ITAJAI ENVIO      01/09 ITAJAI .ENVIO
```

Faixa padrão: de 10 dias atrás até 110 dias à frente, para cada destino de
`CONFIG.DESTINOS`. O botão **Buscar mais fundo** amplia para 90 dias atrás e 240
à frente. São centenas de leituras minúsculas, disparadas 10 a 10, com barra de
progresso — leva poucos segundos.

Aba de data **passada** que não existe fica em cache permanente (não vai nascer).
Data de hoje/futuro fica em cache por 30 minutos, porque aba nova aparece a
qualquer momento. **Atualizar** ignora todo o cache.

### A armadilha do gviz

Pedir uma aba inexistente **não dá erro**: o Google devolve a **primeira aba** da
planilha. Uma varredura ingênua serviria os dados de outro envio como se fossem
do envio pedido — foi assim que nasceu um envio fantasma de 15/09 numa versão
anterior.

Com o CRONOGRAMA na primeira posição, a distinção é trivial: se a resposta é um
cronograma, a aba não existe. Como segunda linha de defesa a página guarda a
**assinatura** da resposta de calibração (primeira célula + cinco primeiros
códigos ML) e descarta respostas idênticas a ela. Se alguém arrastar uma aba de
envio para a primeira posição, a página avisa na tela.

O CRONOGRAMA, por ser a primeira aba, é lido **sem** esse guarda — ali receber
"a primeira aba" é o resultado certo.

## Datas e identificação

A data do envio é a do **nome da aba**, sempre. O cabeçalho `DATA DO ENVIO` não é
cruzado com nada (fica desatualizado em muitas abas); dele vem só o `Nº do Envio`,
que entra no nome do arquivo:

```
SEPARACAO_FULL_1312_ITAJAI_56911677.csv
```

Aba sem ano no nome recebe o ano que deixa a data mais próxima de hoje.

## Extração para o WMS

Formato exigido pelo sistema, inalterado:

```
codigoProduto;quantidade;IDFULL
```

separador `;`, **ISO-8859-1**, sem BOM. Como o sistema não aceita duas linhas do
mesmo produto e um SKU pode ter mais de um código ML, cada arquivo leva **no
máximo uma linha por SKU** — as ocorrências extras vão para `_parte2`, `_parte3`,
enviadas uma por vez. Nunca somar quantidades de códigos ML diferentes: o código
ML é o anúncio de destino.

Um clique em **Baixar N lotes** no cartão do envio baixa todos os arquivos dele.

## Conferência automática

- soma das linhas × `TOTAL DE PRODUTOS` da aba, com o delta
- linhas não lidas (nada é descartado em silêncio)
- código universal repetido entre SKUs diferentes — se a extração agrupar por
  EAN, as unidades saem somadas num produto só
- EAN fora do padrão e rótulo `SKU` torto (`SKULIXADEIRA:` → `LIXADEIRA-…`)

## Relatório

Botões **7 / 15 / 30 dias / tudo**, fechando em hoje (envio futuro aparece no
painel mas não entra no relatório). Traz unidades, envios, dias com envio,
linhas, arquivos, média diária, quebra por destino, lista de envios e SKUs de
maior volume. Exporta em CSV.

## Se algo parar de funcionar

| Sintoma | Causa provável |
|---|---|
| Nenhum envio aparece | planilha deixou de ser pública, ou os nomes das abas mudaram de padrão |
| Envio que não existe aparece | uma aba de envio virou a primeira aba (a página avisa) |
| Um envio conhecido não aparece | data fora da faixa → **Buscar mais fundo**; ou nome fora das duas grafias → *Carregar uma data específica* ou entrada manual |
| Total não bate | ver o bloco de conferência do envio |

A **entrada manual** (colar as células ou arrastar um `.csv`) funciona em
qualquer cenário, inclusive com a planilha privada ou fora do ar.
