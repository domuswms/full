# Separador Full — publicação no GitHub Pages

Site de uma página, sem backend. Lê as abas da planilha do full direto do navegador
e gera as listas de separação em ISO-8859-1.

## Subir / atualizar

1. No repositório do GitHub Pages (o mesmo do dashboard de KPIs, `domuswms.github.io`),
   crie a pasta `full/`.
2. Envie `index.html` para dentro dela — pela interface web: **Add file → Upload files**,
   arrastar o arquivo, **Commit changes**.
3. Fica no ar em `https://domuswms.github.io/full/` em cerca de um minuto.

Atualizar depois é o mesmo caminho: subir o `index.html` novo por cima. O GitHub Pages
guarda cache curto — se a versão antiga insistir, `Ctrl+F5`.

## Pré-requisito na planilha

A planilha precisa estar compartilhada como **"qualquer pessoa com o link — leitor"**.
Sem isso o navegador não consegue ler as abas e a página avisa na tela.

Planilha usada como base (id fixo no `CONFIG`, dentro do `index.html`):

```
16DLNehsisZT8EgAsphM4l0Q6QsWFHiPAMpIFhEJfUqc
PREPARAÇÃO FULL PR /SC
```

Abas lidas:

- `CRONOGRAMA` — datas, destino, modo de envio, motorista
- abas de envio, pelo padrão **`dd/mm Destino Envio`** (ex.: `10/09 Itajaí Envio`)

Se o padrão de nome das abas mudar, ajuste `CONFIG.padraoAba` no topo do arquivo.
Se a planilha mudar, ajuste `CONFIG.SHEET_ID`.

## Uso

1. **Carregar da planilha** — destino + data do envio → *Carregar envio*. A página monta
   o nome da aba, lê e processa. *Atualizar cronograma* recarrega a primeira aba.
2. **Importar aba (alternativa manual)** — colar as células ou arrastar um `.csv`,
   para quando a aba tiver nome fora do padrão ou a planilha estiver privada.
3. **Lotes** — um arquivo por lote, no máximo uma linha por SKU
   (`codigoProduto;quantidade;IDFULL`, `;`, ISO-8859-1, sem BOM).
4. **Conferência** — bate a soma das linhas contra o `TOTAL DE PRODUTOS` da aba e
   lista linhas não lidas, códigos universais repetidos entre SKUs e EANs fora do padrão.

Os envios importados ficam salvos no navegador de quem usa (localStorage), com backup
em `.json`. Não há dado da operação no repositório — só o código da página.

## Formatos de aba reconhecidos

As abas do full existem em duas formas, e as duas são lidas:

- produto na **linha seguinte** ao bloco `Código ML: … SKU: …`
- produto na **mesma linha**, depois do SKU

Também é tolerado o rótulo torto que aparece em algumas linhas
(`SKULIXADEIRA: -MLP750-…` → `LIXADEIRA-MLP750-…`), sempre com aviso na conferência.
