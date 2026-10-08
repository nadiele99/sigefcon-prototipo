# Ajuste do prompt — componentes no padrão shadcn

Este ajuste **substitui a seção 7 ("Componentes que acompanham o layout")** do prompt de layout do protótipo. As seções 1 a 6 (estrutura, sidebar, topnav, breadcrumb, `.content`, `.card`, `.title-band` sobreposta, footer e tokens de estrutura) **continuam exatamente iguais**.

## Regra geral

- **Estrutura não muda:** sidebar, topnav, breadcrumb, fundo do `.shell`, `.card` com `.title-band` (`top:-20px`), `.steps-tab.compact` da faixa e footer.
- **Tudo que é componente de informação segue o shadcn/ui** (referência: shadcnstudio.com/components e o template Propxyz): botões, campos, buscas, selects, checkboxes, tabelas, cards, stat cards, badges, alertas, diálogos, toasts, paginação, estados vazios.
- **Paleta:** branco, cinzas neutros (zinc) e o azul SEDUC (`--navy-700` como cor primária). Verde, âmbar e vermelho só em tons suaves, para status.
- **Elementos isolados e "flutuantes":** dentro de `.card > .body`, o conteúdo é organizado em blocos independentes (`.ui-card`), empilhados com `gap: 20px` (`.stack`). Nada de conteúdo solto sem contêiner.
- **Fonte dos componentes:** Inter (Google Fonts), aplicada só em `.card .body`, diálogos e toasts. A estrutura mantém Segoe UI.

## Tokens de componente (adicionar ao `:root`)

```css
--fg:#18181B; --muted-fg:#71717A; --placeholder:#A1A1AA;
--border:#E4E4E7; --input:#E4E4E7; --muted-bg:#F4F4F5; --subtle:#FAFAFA;
--primary:var(--navy-700); --primary-hover:var(--navy-600);
--ring:rgba(42,78,133,.22);
--r-sm:6px; --r-md:8px; --r-lg:10px; --r-xl:14px;
--shadow-xs:0 1px 2px rgba(16,24,40,.05);
--shadow-sm:0 1px 3px rgba(16,24,40,.06),0 1px 2px rgba(16,24,40,.04);
--shadow-lg:0 12px 32px rgba(16,24,40,.14);
```

## Especificação dos componentes

| Componente | Classe | Medidas e regras |
|---|---|---|
| Botão | `.btn` + variante | Altura 36px, `padding 0 16px`, raio 8px, 14px **peso 500**, ícone 16px, gap 8px. Foco: anel de 3px `--ring`. |
| Variantes | `.btn-primary` `.btn-outline` `.btn-secondary` `.btn-ghost` `.btn-destructive` `.btn-link` | Primary = navy; outline = branco com borda `--border` e `shadow-xs`; ghost = sem fundo, hover `--muted-bg`. **Não usar botão verde** para salvar: a ação principal é sempre `primary`. |
| Tamanhos | `.btn-sm` / `.btn-icon` | 32px de altura; ícone quadrado 32×32. Ações de tabela usam `.btn-ghost.btn-icon` (cinza, hover escurece; `.danger` fica vermelho no hover). |
| Label | `.field > label` | 14px, peso 500, cor `--fg`, **sem caixa alta**. Obrigatório: `*` vermelho logo após. |
| Input / Select / Textarea | `.field input` etc. | Altura 36px, raio 8px, borda 1px `--input`, `padding 0 12px`, 14px, `shadow-xs`. Foco: borda `--navy-500` + anel 3px. Select com chevron duplo (↕) desenhado em SVG. Somente leitura: fundo `--subtle`. |
| Dica | `.hint` | 13px, `--muted-fg`, abaixo do campo. |
| Busca | `.search` | Input com ícone de lupa 16px à esquerda (`padding-left 34px`). |
| Checkbox | `.cb` | 16×16, raio 4px; marcado = fundo navy com check branco. |
| Card | `.ui-card` | Fundo branco, borda 1px `--border`, raio 14px, `shadow-sm`. Cabeçalho `.ui-card-h`: ícone em caixa 36px com borda + título 15px/600 + descrição 13,5px cinza. Conteúdo `.ui-card-c`: `padding 20px 24px 24px`. |
| Stat card | `.ui-card.stat` dentro de `.stats` | Grade automática (mín. 210px). Rótulo 14px/500 à esquerda e ícone em caixa 32px com tom suave à direita; valor 28px/600 com `letter-spacing -.02em`; badge de contexto embaixo (modelo do card "Payments" do Propxyz). |
| Badge | `.badge` + `success` `warning` `danger` `info` `neutral` `outline` | Altura 22px, raio 6px, 12px/500, fundo suave + borda suave da mesma cor. Status usam um ponto (`.dot`) antes do texto. |
| Alerta | `.alert` + `info` `warn` `danger` | Grade de 2 colunas (ícone 18px + texto), raio 10px, `padding 12px 16px`. Título 14px/500 e descrição 13,5px cinza. Substitui o antigo `.note`. |
| Tabela | `.ui-card.table-card` | Barra superior `.tc-toolbar` (busca 280px + filtros + ação primária à direita). Cabeçalho 40px de altura, 13px/500, cinza, **sem caixa alta**, fundo `--subtle`. Células com `padding 12px 16px` e 14px. Hover da linha: `--subtle`. Texto principal 500 + subtexto 13px cinza. Ações alinhadas à direita. |
| Paginação | `.tc-footer` | "Mostrando X de Y …" à esquerda; à direita "Anterior", página ativa (32px, navy) e "Próxima". |
| Estado vazio | `.empty` | Ícone em caixa 40px cinza + título 500 + descrição cinza, centralizados. |
| Lista de detalhes | `.dl` | Grade de 3 colunas: rótulo 13px cinza, valor 14px/500. |
| Item | `.item` | Linha com mídia 40px (fundo azul suave) + título/descrição + ações. `.item.dashed` para estado "não vinculado". |
| Progresso | `.progress` | 8px, raio total, trilho `--muted-bg`, preenchimento navy (vermelho se exceder). |
| Diálogo | `.overlay` + `.dialog` | Fundo `rgba(9,9,11,.5)`; caixa branca com raio 14px, `padding 24px`, `shadow-lg`, máx. 520px. Título 18px/600 + descrição 14px cinza; botão X ghost no canto; rodapé com botões alinhados à direita. Exclusões usam diálogo com `.btn-destructive` (sem `confirm()` nativo). |
| Toast | `.toast` | Estilo Sonner: branco, borda, raio 10px, sombra suave, ícone de check verde ou alerta vermelho, canto inferior direito. |
| Separador | `.sep` | Linha de 1px `--border` antes da barra de ações do formulário. |

## Organização das telas

- **Listagem:** alerta de regra de negócio → linha de stat cards → card de tabela (busca, filtros e botão primário na própria barra da tabela).
- **Formulário:** um `.ui-card` por grupo de campos (ícone + título + descrição), grade de 4 colunas, `.sep` e barra de ações (Cancelar à esquerda; ações secundárias outline e principal primary à direita).
- **Detalhe:** `.title-band.band-light` com as abas compactas (estrutura) e, no corpo, cards de dados (`.dl`), itens de vínculo, stat cards e tabelas.
- **Ícones:** Lucide, sempre chamados por uma função protegida (`icons()`), para a tela não quebrar se o CDN falhar.
