# SIGEFCON · Protótipo de baixa fidelidade

Protótipo clicável do redesenho da área **Oferta formativa** do SIGEFCON (SEDUC-AM): Painel, Formações, Turmas e Encontros.

- Um único arquivo `index.html` (HTML + CSS + JS, sem build). Abra direto no navegador.
- Dados fictícios em memória; "Reiniciar dados" na barra superior restaura o estado inicial.
- Componentes no padrão shadcn/ui; estrutura de layout descrita em `docs/`.

## Regras de negócio refletidas
- Formação é catálogo (existe sem turma); cada turma oferta **uma** formação (`TURMA.id_curso`).
- Encontros pertencem à turma (local e período da turma).
- Ocupação de vagas é calculada na turma; formação mostra alcance.
- Acompanhamento da turma: frequência por período e 4 avaliações por turma (provisório).

## Publicação
Deploy estático na Vercel (sem build).
