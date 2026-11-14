# Plano de trabalho — Seminário de Diferenciação Automática

Prazo: 2026-10-01 a 2026-10-31. Apresentação de 30 a 60 minutos.

## Fio condutor (a resposta à pergunta central)

1. **Diferenças finitas**: o erro total é truncamento + arredondamento,
   `E(h) ≈ C·h^p + ε·|f|/h`. O termo `u/h` vem do cancelamento catastrófico em
   `f(x+h) − f(x)`. Minimizando, `h* ~ u^{1/(p+1)}` e o melhor erro é
   `~ u^{p/(p+1)}`: nunca chega a `u` para nenhum `p` finito.
2. **Passo complexo**: `f'(x) ≈ Im f(x+ih)/h`. Não há subtração, logo não há
   termo `u/h`; o truncamento `h²f'''/6` some para `h` pequeno (ex.: 1e-30) e o
   erro fica `~u`.
3. **AD (números duais / modo direto)**: aplica a regra da cadeia exatamente em
   cada operação elementar; não há `h` nem truncamento. Erro `~u`.
4. **Custo em generalidade**:
   - passo complexo exige `f` analítica *e* código "complex-safe" (sem `abs`,
     `conj`, comparações ingênuas); mas atravessa bibliotecas compiladas que
     aceitam complexos (LAPACK, `np.linalg.solve`);
   - duais exigem que todo o programa seja escrito em operações sobrecarregadas;
     não atravessam código compilado (LAPACK com `dtype=object` falha);
   - ambos dão derivada unilateral em pontos de não-diferenciabilidade (`abs`);
   - 2ª derivada pelo passo complexo volta a ter subtração → volta o cancelamento.
5. **Custo em implementação/tempo**: modo direto (FD, complexo, dual) custa
   O(n) avaliações para um gradiente em R^n; modo reverso custa O(1)
   avaliações (teorema da "cheap gradient"), ao preço de memória/fita.

## Entregáveis (em `/mnt/project-files/seminario-ad/`)

| Entregável | Arquivo | Cobre |
|---|---|---|
| Notebook de experimentos (PyTorch) | `notebook/experimentos_ad.ipynb` | B1, B2, C1, C2, C3 + verificação numérica de A1–A3 |
| Relatório (LaTeX) | `relatorio/relatorio.tex` (+ PDF) | A1–A3, discussão de B e C, figuras do notebook |
| Slides (Beamer) | `apresentacao/` | roteiro de 30–60 min |

## Cronograma sugerido

| Semana | Datas | Tarefas |
|---|---|---|
| 1 | 01–07/10 | Ler Squire–Trapp (1998), Martins et al. (2003) e Baydin et al. (2018, seções 2–3). Rodar e entender o notebook (versão inicial já pronta). |
| 2 | 08–14/10 | Revisar as deduções da Parte A no relatório; refazer à mão. Ajustar experimentos (outras funções, outros x). |
| 3 | 15–21/10 | Fechar relatório (discussão, conclusões). Montar slides. |
| 4 | 22–28/10 | Ensaios cronometrados da apresentação; cortar/expandir para caber em 30–60 min. |
| — | 29–31/10 | Folga e entrega. |

## Roteiro da apresentação (~45 min)

1. Motivação e pergunta central (3 min)
2. Diferenças finitas e o "V" do erro em log-log (8 min) — figura C1
3. Passo complexo: dedução e por que não há `u/h` (8 min)
4. Números duais e AD modo direto (8 min) — demo da classe `Dual`
5. Limites: `abs`, `np.linalg.solve`, segunda derivada (8 min) — tabela B2, figura A3
6. Gradientes em R^n: custo O(n) vs modo reverso O(1) (6 min) — figura C2
7. Conclusões e perguntas (4 min)
