# Diferenciação automática e passo complexo

Trabalho de cunho avaliativo para a disciplina Computação Científica orientada pelo professor Heudson Mirandola do IM-UFRJ: seminário sobre diferenciação automática (AD), passo complexo e diferenças finitas.

**Pergunta central:** por que diferenças finitas nunca atingem erro da ordem do arredondamento unitário *u*, e o que exatamente o passo complexo e a AD fazem para atingi-lo? Qual é o custo em generalidade (tipos de *f* aceitos) e em implementação?

## Conteúdo

| Pasta | O que tem |
|---|---|
| `relatorio/` | Relatório completo (`relatorio.tex` / `.pdf`), recorte só com a teoria (`relatorio_teoria.tex` / `.pdf`) e as figuras geradas pelo notebook (`figuras/`) |
| `notebook/` | `Automatic_Differentiation_Revisado.ipynb` (notebook de Lucas Carvalhosa Lago, base do relatório e dos slides): classe `Dual`, passo complexo, experimentos B1–B2, A3, C1–C3 e D1 (NumPy, JAX, PyTorch). `experimentos_ad.ipynb` é a versão anterior |
| `apresentacao/` | Slides em Beamer (`slides.tex` / `slides.pdf`), roteiro de cerca de 45 minutos |
| `revisao/` | Revisão da parte teórica |
| `PLANO.md` | Plano de trabalho e roteiro da apresentação |

## Como compilar

Requer uma distribuição TeX com `beamer`, `babel` (português) e `latexmk`
(no Debian/Ubuntu: `texlive-latex-extra texlive-lang-portuguese latexmk`).

```bash
cd relatorio && latexmk -pdf relatorio.tex
cd apresentacao && latexmk -pdf slides.tex   # usa as figuras de ../relatorio/figuras
```

Para limpar os arquivos auxiliares: `latexmk -c`.

## Como rodar o notebook

```bash
pip install numpy matplotlib torch "jax[cpu]" jupyter
jupyter notebook notebook/Automatic_Differentiation_Revisado.ipynb
```

As figuras do relatório e dos slides são geradas pelo notebook em `relatorio/figuras/notebook_original/`.

## Referências principais

- W. Squire e G. Trapp, *Using complex variables to estimate derivatives of real functions*, SIAM Review, 1998.
- J. R. R. A. Martins, P. Sturdza e J. J. Alonso, *The complex-step derivative approximation*, ACM TOMS, 2003.
- A. G. Baydin et al., *Automatic differentiation in machine learning: a survey*, JMLR, 2018.
