# PROMPT — Migrar os componentes do protótipo para o padrão shadcn

> Cole este prompt no outro projeto junto com o arquivo HTML atual do protótipo.

## Contexto

Este projeto tem um protótipo de baixa fidelidade em **um único arquivo HTML** (HTML + CSS + JS vanilla, ícones Lucide), com o layout de sidebar azul-marinho, topnav branca, breadcrumb, cartão branco com **faixa de título azul sobreposta** (`.title-band` com `top:-20px`) e rodapé com faixa de gradiente.

Preciso **refinar visualmente todos os componentes de informação** para seguirem o padrão **shadcn/ui**, usando como referência o shadcn studio (https://shadcnstudio.com/components) e o template Propxyz (https://shadcn-nextjs-propxyz-admin-template.vercel.app/agent/dashboard, /agent/account e /agent/visits): limpo, elegante, elementos isolados em cards "flutuantes", tipografia leve (peso 500 em vez de negrito) e bordas finas.

Continua sendo baixa fidelidade, mas com exigência visual alta.

## O que NÃO muda (estrutura)

Não altere nada disto (CSS, medidas, cores e comportamento):

- `.proto-bar` (barra do protótipo), `.shell`, `.sidebar` e seus botões de etapa/menu
- `.topnav`, `.breadcrumb`, fundo do `.shell` (`--bg`)
- `.content` (padding `34px 16px 24px`), `.card`, `.card .body` (padding 24px)
- `.title-band` (inclusive `position:relative; top:-20px`), `.title-band.band-light`, `.band-left`, `.band-dot`, `.band-sub`, `.tag` e as abas compactas `.steps-tab.compact` dentro da faixa
- `.footer` e a faixa de gradiente
- Os tokens de estrutura do `:root` (`--navy-*`, `--gray-*`, `--bg`, etc.) e a fonte da estrutura (Segoe UI)
- Toda a lógica JS, os dados, as regras de negócio, as telas e a navegação

## O que muda (componentes)

Tudo o que fica **dentro de `.card > .body`**, nos modais e nos toasts: botões, campos, buscas, selects, checkboxes, tabelas, indicadores/KPIs, badges, avisos, listas de detalhe, modais, toasts, paginação e estados vazios.

### Regras visuais

1. **Paleta:** branco, cinzas neutros e o azul SEDUC (`--navy-700`) como cor primária. Verde, âmbar e vermelho **somente em tons suaves** e para status. Nenhum botão verde: a ação principal é sempre `.btn-primary` (azul).
2. **Fonte dos componentes:** Inter (Google Fonts), aplicada só em `.card .body`, `.overlay` e `#toast`.
3. **Elementos isolados:** dentro de `.card .body`, envolva o conteúdo em `<div class="stack">` e organize tudo em blocos independentes (`.ui-card`), com 20px entre eles. Nada de conteúdo solto.
4. **Textos:** rótulos e cabeçalhos de tabela **sem caixa alta**, peso 500. Títulos de card 15px/600. Textos secundários 13–13,5px em `--muted-fg`.
5. **Tamanhos shadcn:** botões e campos com 36px de altura, raio 8px. Botões pequenos e de ícone com 32px. Cards com raio 14px. Badges com 22px e raio 6px.

### Como organizar cada tipo de tela

- **Listagem:** alerta de regra de negócio (`.alert.info`) → linha de indicadores (`.stats` com `.ui-card.stat`) → card de tabela (`.table-card`) com busca, filtros e botão primário na barra superior, paginação no rodapé.
- **Formulário:** um `.ui-card` por grupo de campos (cabeçalho com ícone, título e descrição + grade `.grid.g4`), depois `<hr class="sep">` e a barra `.form-actions` (Cancelar à esquerda; ação secundária `outline` e principal `primary` à direita).
- **Detalhe:** dados em `.dl` dentro de um `.ui-card`; vínculos em `.item` (ou `.item.dashed` quando vazio); indicadores e tabelas como na listagem.
- **Ações de linha da tabela:** `.btn.btn-ghost.btn-icon` (lápis, olho, link, lixeira). A lixeira usa `.danger`.
- **Exclusões:** abrir um modal de confirmação com `.btn-destructive`. Não usar `confirm()` do navegador.
- **Mapeamento das classes antigas:** `.note` → `.alert.info`; `.note.warn` → `.alert.warn`; `.kpi` → `.ui-card.stat`; `.btn-navy`/`.btn-green` → `.btn-primary`; badges `green/yellow/red/gray` → `success/warning/danger/neutral` (status com `<span class="dot"></span>`); modal com cabeçalho azul → `.dialog` branco.

## 1. Adicionar ao `:root` (sem remover os tokens existentes)

```css
/* Tokens de componente (padrão shadcn, paleta branco/cinza/azul SEDUC) */
  --fg:#18181B; --muted-fg:#71717A; --placeholder:#A1A1AA;
  --border:#E4E4E7; --input:#E4E4E7; --muted-bg:#F4F4F5; --subtle:#FAFAFA;
  --primary:var(--navy-700); --primary-hover:var(--navy-600);
  --ring:rgba(42,78,133,.22);
  --r-sm:6px; --r-md:8px; --r-lg:10px; --r-xl:14px;
  --shadow-xs:0 1px 2px rgba(16,24,40,.05);
  --shadow-sm:0 1px 3px rgba(16,24,40,.06),0 1px 2px rgba(16,24,40,.04);
  --shadow-lg:0 12px 32px rgba(16,24,40,.14);
```

## 2. Adicionar no `<head>` e na lógica de ícones

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>
<script>window.lucide||document.write('<script src="https://cdn.jsdelivr.net/npm/lucide@latest/dist/umd/lucide.min.js"><\/script>')</script>
```

Troque toda chamada `lucide.createIcons()` por `icons()`, para a tela não quebrar se o CDN falhar:

```js
function icons(){ if (window.lucide && lucide.createIcons) lucide.createIcons(); }
window.addEventListener('load', icons);
```

## 3. Substituir o CSS dos componentes antigos por este

Remova as regras antigas de `.btn*`, `.actions-row`, `.kpi*`, `.badge*`, `.note*`, `.field*`, `th`, `td`, modais e toasts, e use:

```css
.card .body,.overlay,#toast{font-family:"Inter","Segoe UI",Roboto,Arial,sans-serif;font-size:14px;color:var(--fg);-webkit-font-smoothing:antialiased;}
.stack{display:flex;flex-direction:column;gap:20px;}
.muted{color:var(--muted-fg);}
svg.lucide{stroke-width:2;}

/* Button */
.btn{display:inline-flex;align-items:center;justify-content:center;gap:8px;height:36px;padding:0 16px;border-radius:var(--r-md);font:500 14px/1 inherit;font-family:inherit;border:1px solid transparent;cursor:pointer;white-space:nowrap;transition:background .15s,border-color .15s,color .15s,box-shadow .15s;outline:none;}
.btn svg{width:16px;height:16px;flex-shrink:0;}
.btn:focus-visible{box-shadow:0 0 0 3px var(--ring);}
.btn:disabled{opacity:.5;pointer-events:none;}
.btn-primary{background:var(--primary);color:#fff;box-shadow:var(--shadow-xs);}
.btn-primary:hover{background:var(--primary-hover);}
.btn-outline{background:#fff;color:var(--fg);border-color:var(--border);box-shadow:var(--shadow-xs);}
.btn-outline:hover{background:var(--muted-bg);}
.btn-secondary{background:var(--muted-bg);color:var(--fg);}
.btn-secondary:hover{background:#E9E9EC;}
.btn-ghost{background:transparent;color:var(--fg);}
.btn-ghost:hover{background:var(--muted-bg);}
.btn-destructive{background:#DC2626;color:#fff;box-shadow:var(--shadow-xs);}
.btn-destructive:hover{background:#B91C1C;}
.btn-sm{height:32px;padding:0 12px;font-size:13px;gap:6px;}
.btn-icon{width:32px;height:32px;padding:0;}
.btn-icon.danger:hover{color:#DC2626;background:#FEF2F2;}
.btn-ghost.btn-icon{color:var(--muted-fg);}
.btn-ghost.btn-icon:hover{color:var(--fg);}
.btn-link{background:none;border:none;padding:0;height:auto;color:var(--navy-600);font-weight:500;cursor:pointer;font:inherit;}
.btn-link:hover{text-decoration:underline;text-underline-offset:3px;}

/* Field / Input / Select / Textarea */
.field{display:flex;flex-direction:column;gap:8px;min-width:0;}
.field > label{font-size:14px;font-weight:500;line-height:1.2;color:var(--fg);}
.req{color:#DC2626;margin-left:2px;}
.hint{font-size:13px;color:var(--muted-fg);line-height:1.4;}
.input,.field input:not([type=checkbox]),.field select,.field textarea,.toolbar select{
  width:100%;height:36px;border:1px solid var(--input);border-radius:var(--r-md);padding:0 12px;font:400 14px/1.4 inherit;font-family:inherit;
  background:#fff;color:var(--fg);box-shadow:var(--shadow-xs);outline:none;transition:border-color .15s,box-shadow .15s;}
.field textarea{height:auto;min-height:88px;padding:8px 12px;resize:vertical;}
.field input::placeholder,.field textarea::placeholder,.input::placeholder{color:var(--placeholder);}
.input:focus,.field input:focus,.field select:focus,.field textarea:focus,.toolbar select:focus{border-color:var(--navy-500);box-shadow:0 0 0 3px var(--ring);}
.field input[readonly]{background:var(--subtle);color:var(--muted-fg);}
.field select,.toolbar select{appearance:none;-webkit-appearance:none;padding-right:34px;cursor:pointer;
  background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='16' height='16' viewBox='0 0 24 24' fill='none' stroke='%2371717A' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3E%3Cpath d='m7 15 5 5 5-5'/%3E%3Cpath d='m7 9 5-5 5 5'/%3E%3C/svg%3E");
  background-repeat:no-repeat;background-position:right 10px center;}
.search{position:relative;display:flex;align-items:center;}
.search svg{position:absolute;left:11px;width:16px;height:16px;color:var(--muted-fg);pointer-events:none;}
.search .input{padding-left:34px;}
.input-group{display:flex;gap:8px;align-items:flex-end;}
.input-group .field{flex:1;}

/* Checkbox */
.cb{appearance:none;-webkit-appearance:none;width:16px;height:16px;border:1px solid #D4D4D8;border-radius:4px;background:#fff;box-shadow:var(--shadow-xs);display:inline-grid;place-content:center;cursor:pointer;margin:0;flex-shrink:0;transition:background .15s;}
.cb:checked{background:var(--primary);border-color:var(--primary);}
.cb:checked::after{content:"";width:8px;height:4px;border:2px solid #fff;border-top:0;border-right:0;transform:rotate(-45deg) translate(1px,-1px);}
.cb:focus-visible{box-shadow:0 0 0 3px var(--ring);outline:none;}
.check-row{display:flex;align-items:center;gap:10px;font-size:14px;cursor:pointer;}

/* Card (componente) */
.ui-card{background:#fff;border:1px solid var(--border);border-radius:var(--r-xl);box-shadow:var(--shadow-sm);}
.ui-card-h{display:flex;align-items:flex-start;gap:12px;padding:20px 24px 0;}
.ui-card-h .ic{width:36px;height:36px;border-radius:var(--r-md);border:1px solid var(--border);box-shadow:var(--shadow-xs);display:grid;place-items:center;color:var(--navy-600);flex-shrink:0;}
.ui-card-h .ic svg{width:18px;height:18px;}
.ui-card-title{font-size:15px;font-weight:600;letter-spacing:-.01em;line-height:1.3;}
.ui-card-desc{font-size:13.5px;color:var(--muted-fg);margin-top:3px;}
.ui-card-h .right{margin-left:auto;}
.ui-card-c{padding:20px 24px 24px;}

/* Stat card */
.stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(210px,1fr));gap:16px;}
.stat{padding:20px;}
.stat-h{display:flex;justify-content:space-between;align-items:flex-start;gap:10px;font-size:14px;font-weight:500;}
.stat-ico{width:32px;height:32px;border-radius:var(--r-md);display:grid;place-items:center;flex-shrink:0;}
.stat-ico svg{width:16px;height:16px;}
.tone-blue{background:#EFF4FB;color:var(--navy-600);} .tone-green{background:#ECFDF3;color:#067647;}
.tone-amber{background:#FFFAEB;color:#B54708;} .tone-red{background:#FEF3F2;color:#B42318;} .tone-gray{background:var(--muted-bg);color:#3F3F46;}
.stat-v{font-size:28px;font-weight:600;letter-spacing:-.02em;line-height:1.1;margin:8px 0 12px;}
.stat-v small{font-size:14px;font-weight:500;color:var(--muted-fg);letter-spacing:0;}

/* Badge */
.badge{display:inline-flex;align-items:center;gap:5px;height:22px;padding:0 8px;border-radius:var(--r-sm);font-size:12px;font-weight:500;white-space:nowrap;border:1px solid transparent;line-height:1;}
.badge svg{width:12px;height:12px;}
.badge .dot{width:6px;height:6px;border-radius:50%;background:currentColor;}
.badge.success{background:#ECFDF3;color:#067647;border-color:#D1FADF;}
.badge.warning{background:#FFFAEB;color:#B54708;border-color:#FEF0C7;}
.badge.danger{background:#FEF3F2;color:#B42318;border-color:#FEE4E2;}
.badge.info{background:#EFF4FB;color:var(--navy-600);border-color:#DCE6F3;}
.badge.neutral{background:var(--muted-bg);color:#3F3F46;border-color:var(--border);}
.badge.outline{background:#fff;color:var(--fg);border-color:var(--border);}

/* Alert */
.alert{display:grid;grid-template-columns:18px 1fr;column-gap:12px;row-gap:2px;padding:12px 16px;border:1px solid var(--border);border-radius:var(--r-lg);background:#fff;line-height:1.5;}
.alert > svg,.alert > i{width:18px;height:18px;grid-row:span 2;grid-column:1;margin-top:1px;}
.alert-title,.alert-desc{grid-column:2;}
.alert.no-title > svg,.alert.no-title > i{grid-row:auto;}
.alert-title{font-weight:500;}
.alert-desc{font-size:13.5px;color:var(--muted-fg);}
.alert-desc b{color:var(--fg);font-weight:500;}
.alert.info{background:#F7F9FC;border-color:#DCE6F3;} .alert.info > svg{color:var(--navy-600);}
.alert.warn{background:#FFFBEB;border-color:#FDE68A;} .alert.warn > svg,.alert.warn .alert-title{color:#B45309;} .alert.warn .alert-desc{color:#92400E;} .alert.warn .alert-desc b{color:#78350F;}
.alert.danger{background:#FEF2F2;border-color:#FECACA;} .alert.danger > svg,.alert.danger .alert-title{color:#B91C1C;}

/* Table */
.table-card{overflow:hidden;}
.tc-toolbar{display:flex;gap:8px;align-items:center;padding:12px 16px;border-bottom:1px solid var(--border);flex-wrap:wrap;}
.tc-toolbar .search{width:280px;max-width:100%;}
.tc-toolbar select{width:auto;min-width:190px;}
.tc-toolbar .right{margin-left:auto;display:flex;gap:8px;}
.table-wrap{overflow-x:auto;}
.table{width:100%;border-collapse:collapse;font-size:14px;}
.table th{height:40px;padding:0 16px;text-align:left;font-size:13px;font-weight:500;color:var(--muted-fg);background:var(--subtle);border-bottom:1px solid var(--border);white-space:nowrap;}
.table td{padding:12px 16px;border-bottom:1px solid var(--border);vertical-align:middle;}
.table tbody tr:last-child td{border-bottom:none;}
.table tbody tr:hover td{background:var(--subtle);}
.table tr.past td{color:var(--muted-fg);}
.table .cell-main{font-weight:500;color:var(--fg);}
.table .cell-sub{font-size:13px;color:var(--muted-fg);margin-top:2px;}
.table td.acts{white-space:nowrap;text-align:right;width:1%;}
.table th.acts{text-align:right;}
.table .num{font-variant-numeric:tabular-nums;}
.tc-footer{display:flex;justify-content:space-between;align-items:center;gap:10px;padding:12px 16px;border-top:1px solid var(--border);font-size:13.5px;color:var(--muted-fg);flex-wrap:wrap;}
.pagination{display:flex;align-items:center;gap:4px;}
.page{width:32px;height:32px;border-radius:var(--r-md);border:1px solid transparent;background:transparent;font:500 13px inherit;font-family:inherit;cursor:pointer;color:var(--fg);}
.page.active{background:var(--primary);color:#fff;}
.empty{display:flex;flex-direction:column;align-items:center;text-align:center;padding:36px 16px;gap:6px;}
.empty .ic{width:40px;height:40px;border-radius:var(--r-lg);background:var(--muted-bg);display:grid;place-items:center;color:var(--muted-fg);margin-bottom:6px;}
.empty .ic svg{width:20px;height:20px;}
.empty b{font-weight:500;font-size:14px;}
.empty span{font-size:13.5px;color:var(--muted-fg);}

/* Layout helpers */
.grid{display:grid;gap:18px 20px;}
.g2{grid-template-columns:repeat(2,minmax(0,1fr));} .g3{grid-template-columns:repeat(3,minmax(0,1fr));} .g4{grid-template-columns:repeat(4,minmax(0,1fr));}
.span2{grid-column:span 2;} .span3{grid-column:span 3;} .span4{grid-column:1/-1;}
.sep{height:1px;background:var(--border);border:0;margin:0;}
.form-actions{display:flex;justify-content:space-between;gap:12px;flex-wrap:wrap;}
.form-actions .group{display:flex;gap:8px;flex-wrap:wrap;}

/* Description list / Item */
.dl{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:20px 24px;margin:0;}
.dl dt{font-size:13px;color:var(--muted-fg);}
.dl dd{margin:4px 0 0;font-weight:500;}
.item{display:flex;align-items:center;gap:14px;padding:16px;border:1px solid var(--border);border-radius:var(--r-lg);flex-wrap:wrap;}
.item .media{width:40px;height:40px;border-radius:var(--r-md);background:#EFF4FB;color:var(--navy-600);display:grid;place-items:center;flex-shrink:0;}
.item .media svg{width:20px;height:20px;}
.item .body-i{flex:1;min-width:220px;}
.item .t{font-weight:500;display:flex;align-items:center;gap:8px;flex-wrap:wrap;}
.item .d{font-size:13.5px;color:var(--muted-fg);margin-top:3px;}
.item .a{display:flex;gap:8px;}
.item.dashed{border-style:dashed;background:var(--subtle);}
.item.dashed .media{background:var(--muted-bg);color:var(--muted-fg);}

/* Progress */
.progress{height:8px;background:var(--muted-bg);border-radius:999px;overflow:hidden;}
.progress > div{height:100%;background:var(--primary);border-radius:999px;}
.progress.over > div{background:#DC2626;}

/* Mini lista (formadores no formulário) */
.mini-list{border:1px solid var(--border);border-radius:var(--r-lg);overflow:hidden;margin-top:16px;}

/* Dialog */
.overlay{position:fixed;inset:0;background:rgba(9,9,11,.5);z-index:300;display:flex;align-items:flex-start;justify-content:center;padding:80px 16px;overflow:auto;animation:fade .15s ease;}
.dialog{position:relative;background:#fff;border:1px solid var(--border);border-radius:var(--r-xl);width:100%;max-width:520px;padding:24px;box-shadow:var(--shadow-lg);display:flex;flex-direction:column;gap:18px;animation:zoom .15s ease;}
.dialog-h h3{margin:0;font-size:18px;font-weight:600;letter-spacing:-.01em;}
.dialog-h p{margin:6px 0 0;color:var(--muted-fg);font-size:14px;line-height:1.5;}
.dialog-x{position:absolute;top:14px;right:14px;}
.dialog-b{display:flex;flex-direction:column;gap:16px;}
.dialog-f{display:flex;justify-content:flex-end;gap:8px;}
.check-list{border:1px solid var(--border);border-radius:var(--r-lg);max-height:220px;overflow:auto;}
.check-list label{display:flex;align-items:flex-start;gap:12px;padding:10px 12px;border-bottom:1px solid var(--border);cursor:pointer;}
.check-list label:last-child{border-bottom:none;}
.check-list label:hover{background:var(--subtle);}
.check-list .cb{margin-top:2px;}
.check-list .t{font-weight:500;} .check-list .s{font-size:13px;color:var(--muted-fg);margin-top:2px;}
@keyframes fade{from{opacity:0}to{opacity:1}}
@keyframes zoom{from{opacity:0;transform:scale(.97)}to{opacity:1;transform:none}}

/* Toast (sonner) */
#toast{position:fixed;right:20px;bottom:20px;z-index:400;display:flex;flex-direction:column;gap:8px;width:360px;max-width:calc(100vw - 40px);}
.toast{display:flex;gap:10px;align-items:flex-start;background:#fff;border:1px solid var(--border);border-radius:var(--r-lg);padding:14px 16px;box-shadow:0 6px 18px rgba(16,24,40,.1);font-size:13.5px;line-height:1.45;animation:zoom .15s ease;}
.toast svg{width:16px;height:16px;margin-top:1px;flex-shrink:0;color:#16A34A;}
.toast.err svg{color:#DC2626;}

@media (max-width:1000px){.g4{grid-template-columns:repeat(2,minmax(0,1fr));}.g3,.dl{grid-template-columns:repeat(2,minmax(0,1fr));}.span3{grid-column:1/-1;}}
```

## 4. Funções auxiliares para gerar a marcação (JS)

Use estas funções para montar alertas, indicadores, cabeçalhos de card, tabelas, busca, toasts e estados vazios de forma consistente. Elas dependem de `$` (atalho para `document.getElementById`), `esc` (escape de HTML) e `ico`; crie as que faltarem. A função `ico(nome)` deve existir: `const ico = (name, extra='') => `<i data-lucide="${name}" ${extra}></i>`;`

```js
/* ---------- Construtores de componentes (shadcn) ---------- */
function alertBox(type, title, text){
  const i = {info:'info', warn:'triangle-alert', danger:'circle-alert'}[type];
  return `<div class="alert ${type}${title?'':' no-title'}">${ico(i)}${title?`<div class="alert-title">${title}</div>`:''}<div class="alert-desc">${text}</div></div>`;
}
function statCard(label, value, icon, tone, badge){
  return `<div class="ui-card stat"><div class="stat-h"><span>${label}</span><span class="stat-ico tone-${tone}">${ico(icon)}</span></div>
    <div class="stat-v">${value}</div>${badge||''}</div>`;
}
function cardHead(icon, title, desc, right){
  return `<div class="ui-card-h"><div class="ic">${ico(icon)}</div><div><div class="ui-card-title">${title}</div>${desc?`<div class="ui-card-desc">${desc}</div>`:''}</div>${right?`<div class="right">${right}</div>`:''}</div>`;
}
function emptyRow(cols, icon, title, desc){
  return `<tr><td colspan="${cols}" style="padding:0"><div class="empty"><div class="ic">${ico(icon)}</div><b>${title}</b><span>${desc}</span></div></td></tr>`;
}
function tableCard({id, toolbar, head, rows, cols, empty, label}){
  const n = rows.length;
  return `<div class="ui-card table-card">
    ${toolbar ? `<div class="tc-toolbar">${toolbar}</div>` : ''}
    <div class="table-wrap"><table class="table" id="${id}"><thead><tr>${head}</tr></thead>
      <tbody>${n ? rows.join('') : emptyRow(cols, ...empty)}</tbody></table></div>
    <div class="tc-footer"><span>Mostrando <span data-count-for="${id}">${n}</span> de ${n} ${label}</span>
      <div class="pagination"><button class="btn btn-ghost btn-sm" disabled>${ico('chevron-left')}Anterior</button><button class="page active">1</button><button class="btn btn-ghost btn-sm" disabled>Próxima${ico('chevron-right')}</button></div></div>
  </div>`;
}
function searchBox(placeholder, tableId){
  return `<div class="search">${ico('search')}<input class="input" placeholder="${placeholder}" oninput="filterRows(this,'${tableId}')"></div>`;
}
function filterRows(input, tableId){
  const q = input.value.trim().toLowerCase(); let n = 0;
  document.querySelectorAll('#'+tableId+' tbody tr[data-text]').forEach(tr => { const ok = tr.dataset.text.includes(q); tr.style.display = ok ? '' : 'none'; if (ok) n++; });
  const c = document.querySelector(`[data-count-for="${tableId}"]`); if (c) c.textContent = n;
}
function opts(list, sel, emptyLabel){
  return (emptyLabel!==undefined ? `<option value="">${emptyLabel}</option>` : '') +
    list.map(o => { const [v,l] = Array.isArray(o) ? o : [o,o]; return `<option value="${esc(v)}" ${v===sel?'selected':''}>${esc(l)}</option>`; }).join('');
}
function toast(msg, type){
  const el = document.createElement('div');
  el.className = 'toast' + (type==='err' ? ' err' : '');
  el.innerHTML = `${ico(type==='err' ? 'circle-alert' : 'circle-check')}<span>${esc(msg)}</span>`;
  $('toast').appendChild(el); icons();
  setTimeout(() => el.remove(), 3800);
}
```

Modal no padrão shadcn (substitui o modal antigo):

```js
function openModal(title, desc, body, footer){
  $('modal-root').innerHTML = `<div class="overlay" onclick="if(event.target===this)closeModal()"><div class="dialog" role="dialog">
    <button class="btn btn-ghost btn-icon dialog-x" onclick="closeModal()" title="Fechar">${ico('x')}</button>
    <div class="dialog-h"><h3>${title}</h3>${desc?`<p>${desc}</p>`:''}</div>
    ${body ? `<div class="dialog-b">${body}</div>` : ''}
    <div class="dialog-f">${footer}</div></div></div>`;
  icons();
}
function closeModal(){ $('modal-root').innerHTML = ''; }
```

## 5. Exemplos de marcação

```html
<!-- Card de formulário -->
<div class="ui-card">
  <div class="ui-card-h"><div class="ic"><i data-lucide="book-open"></i></div>
    <div><div class="ui-card-title">Dados da formação</div><div class="ui-card-desc">Identificação e conteúdo</div></div></div>
  <div class="ui-card-c"><div class="grid g4">
    <div class="field span2"><label>Título<span class="req">*</span></label><input placeholder="Ex.: Excel Avançado"><span class="hint">Texto de ajuda</span></div>
    <div class="field"><label>Modalidade</label><select><option>Presencial</option></select></div>
  </div></div>
</div>

<!-- Badge de status -->
<span class="badge success"><span class="dot"></span>Ativa</span>

<!-- Botões -->
<button class="btn btn-primary"><i data-lucide="plus"></i>Nova turma</button>
<button class="btn btn-outline">Cancelar</button>
<button class="btn btn-ghost btn-icon danger" title="Excluir"><i data-lucide="trash-2"></i></button>
```

## 6. Checklist de entrega

- [ ] Estrutura (sidebar, topnav, breadcrumb, faixa de título sobreposta, rodapé) idêntica à anterior
- [ ] Nenhum botão verde, nenhum rótulo em caixa alta, nenhum `confirm()`/`alert()` nativo
- [ ] Todas as telas com o conteúdo do corpo dentro de `.stack` e de `.ui-card`
- [ ] Tabelas com barra de busca, cabeçalho cinza claro, ações em ícones fantasma e paginação
- [ ] Avisos de regra de negócio como `.alert`
- [ ] Toda a lógica e navegação funcionando como antes; sem `localStorage`
- [ ] A página não quebra se o Lucide não carregar
