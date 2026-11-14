# Revisão da parte teórica (relatorio_teoria.tex)

Revisão de 2026-10-15. As correções valem também para `relatorio.tex`, do qual este é um recorte. Nenhum .tex foi alterado.

## Resumo

Todas as fórmulas de erro, os passos ótimos e as demonstrações principais estão **corretos**. Conferi cada derivação à mão e os números com `mpmath` (50 dígitos) e `sympy`, em f(x)=e^x/(cos³x+sin³x), x=1,5. Encontrei **um erro conceitual** (a afirmação de que a segunda derivada exige subtrair), **um número otimista** na Seção A3 e alguns pontos de rigor e notação.

## O que foi conferido e está correto

| Item | Verificação |
|---|---|
| u = 2⁻⁵³ ≈ 1,11e-16 | ok (arredondamento ao mais próximo) |
| Progressiva: h* = 2√(u\|f\|/\|f''\|), E* = 2√(u\|f\|\|f''\|) | derivado à mão, ok |
| Centrada: truncamento h²f'''/6 + h⁴f⁽⁵⁾/120; h* = (3u\|f\|/\|f'''\|)^{1/3} | ok |
| Richardson: −h⁴f⁽⁵⁾/480; arredondamento 8/3+1/3 = 3u\|f\|/h; h* = (360u\|f\|/\|f⁽⁵⁾\|)^{1/5} | ok |
| Expansão de f(x+ih), partes real e imaginária, fórmula do passo complexo | ok |
| "mesmo módulo, sinal oposto, 9,472e-6 em h=1e-3" | ok: −9,47244e-6 (complexo) e +9,47247e-6 (centrada); teoria h²f'''/6 = 9,47245e-6 |
| h ≤ √(6u\|f'\|/\|f'''\|) ≈ 6,5e-9 | ok: 6,516e-9 |
| Regras do produto e do quociente nos duais, (bc−ad)/c² | ok |
| Expansão "quase dual" com δ² = σ (todos os coeficientes) | ok |
| A3: truncamento h²f⁽⁴⁾/12, arredondamento 4u\|f\|/h², h* = (48u\|f\|/\|f⁽⁴⁾\|)^{1/4} | ok (h* = 1,03e-4) |
| Mesmo balanço de erro da centrada de 2ª ordem | ok |
| Hiperdual: coeficiente de ε₁ε₂ é f'' | ok; f''(1,5) = 14,56828426829999154 (sympy), erro do notebook 4,9e-16 confere |

## Problemas encontrados

### 1. (Importante) "Isolar f'' exige subtrair" vale só para passo puramente imaginário
**Onde:** Seção 4, último parágrafo ("A mesma equação explica o limite...") e Seção 5, parágrafo "O que isso diz sobre os limites do truque" ("Um único i só isola uma derivada").

**Problema:** a conclusão só vale para o passo x+ih. Com um passo complexo **inclinado**, ω = e^{iπ/4} (ω² = i), os termos pares passam para a parte imaginária:

Im[f(x+ωh) + f(x−ωh)] / h² = f''(x) − (h⁴/360) f⁽⁶⁾(x) + …

Não há termo O(1) a remover (Lai e Crassidis, 2008). Verifiquei numericamente: erro relativo 1,9e-12 com h = 1e-3, contra 2,6e-8 no melhor caso da fórmula da parte real. Ainda há cancelamento (os termos ±h f'/√2 das duas avaliações se anulam), só que de grandezas O(h) em vez de O(1). Por isso o método também não chega a u, mas fica bem melhor que u^{1/2}.

**Correção proposta:** trocar "Um único i só isola uma derivada" por algo como: "Com passo puramente imaginário, só f' fica livre de cancelamento. Passos inclinados (ω = e^{iπ/4}) recuperam f'' sem subtrair termos O(1), mas ainda cancelam termos O(h). Para derivadas superiores exatas são necessárias várias unidades independentes (multicomplexos) ou duais aninhados." Também acrescentar à bibliografia: K.-L. Lai e J. L. Crassidis, *Extensions of the first and second complex-step derivative approximations*, J. Comput. Appl. Math. 219 (2008) 276–293. Isso rende uma boa pergunta para a apresentação.

### 2. (Número) "ambos atingem cerca de 5×10⁻⁹ em h≈10⁻⁴" (Seção 5)
O notebook informa o mínimo de uma curva ruidosa (4,6e-9 e 6,1e-9). A previsão teórica para o erro relativo ótimo é 2√(u\|f\|\|f⁽⁴⁾\|/3)/\|f''\| ≈ **2,6e-8**. Numa varredura, a mediana perto de h = 1e-4 é cerca de 3e-8. O valor 5e-9 é um mínimo "sortudo", em que os erros de arredondamento se cancelaram.
**Correção:** "ambos atingem erro relativo da ordem de 10⁻⁸ (previsão: 2,6×10⁻⁸) em h ≈ 10⁻⁴, com mínimos pontuais de até 5×10⁻⁹ por cancelamento fortuito de arredondamentos."

### 3. (Precisão) "para h < u|x| tem-se fl(x+h) = x" (Seção 2.4)
A condição exata é h abaixo de meio ulp de x, ou seja, h < u·2^⌊log₂|x|⌋, que pode ser menor que u|x|. Contraexemplo: x = 1,5, h = 1,2u, e fl(1,5+h) ≠ 1,5.
**Correção:** "para h ≲ u|x| (mais precisamente, abaixo de meio ulp de x)". Também vale mencionar que, para h um pouco maior, o passo efetivamente usado é fl(x+h)−x ≠ h. Isso dá outro erro do tipo u|x|/h, que o modelo omite e que se corrige com o truque h ← (x+h)−x.

### 4. (Rigor) Proposição das diferenças finitas
- É apresentada como proposição, mas a "demonstração" é o argumento heurístico do modelo de erro. O certo é dizer "no modelo de erro acima" ou chamar de observação.
- Falta a hipótese C_p ≠ 0. Se f é um polinômio de grau ≤ p, o truncamento é zero e h grande dá erro O(u). Sugestão: "supondo f^{(p+1)}(x) ≠ 0".
- "Nenhuma fórmula atinge O(u)" é uma afirmação sobre o expoente. Com constantes favoráveis, Richardson já dá cerca de 8e-13 neste exemplo. Vale uma frase dizendo isso.

### 5. (Rigor) Prova da proposição dos duais, Passo 1
- O texto pede só que φ seja "diferenciável" e depois usa "expansão de Taylor em duas variáveis" com termos de ordem ≥2. Para φ só diferenciável, essa série não existe. **Correção:** definir φ em duais pela fórmula de primeira ordem φ(a+bε, c+dε) := φ(a,c) + [∂₁φ·b + ∂₂φ·d]ε, como já é feito para g na Seção 4.2, e dizer que, para φ analítica, isso coincide com a série truncada.
- Falta dizer que i, k < j (cada variável usa apenas variáveis já calculadas). A indução depende disso.
- A potência com expoente dual (a+bε)^(c+dε) tem ∂₂φ = a^c log a e exige a > 0. Vale uma nota curta.

### 6. (Precisão) Laços e desvios: "derivada exata do caminho percorrido"
A frase está correta, mas falta a ressalva: ela só coincide com f'(x) se o caminho for o mesmo numa vizinhança de x. Exemplo: `if x == 0: return 0 else: return x` dá derivada 0 em x = 0, mas f(x) = x. Sugiro uma frase com esse exemplo.

### 7. (Exagero) Parágrafo "Erro" dos duais
"o melhor possível para qualquer método que use aritmética de precisão u" não é demonstrado. **Correção:** "da mesma ordem do erro com que se calcula a própria f, o que em geral não se pode melhorar sem aumentar a precisão."

### 8. (Notação) Conflito de u
Na Seção 4.1, "a = u(x), b = u'(x)" usa u, que no texto inteiro é o arredondamento unitário. **Correção:** usar p(x), q(x) (h e g já têm outro papel no texto).

### 9. (Redação) Passo complexo: "o truncamento já é menor que u"
O critério dado é h²|f'''|/6 ≤ u|f'|, ou seja, erro **relativo** menor que u. **Correção:** "menor que u|f'|" ou "erro relativo de truncamento menor que u". Na mesma linha, vale observar que o 9,472×10⁻⁶ da Seção 3 é erro **absoluto** (o relativo é 2,6×10⁻⁶).

### 10. (Menor) h = 10⁻²⁰⁰ no passo complexo
É seguro para a maioria das funções, mas qualquer operação que forme h² no caminho (módulo complexo, z·z̄) dá underflow. Isso reforça o ponto do `abs` no notebook. Se quiser, uma nota de rodapé.

## Sem problemas
Abstract, introdução, modelo de ponto flutuante, argumento de Sterbenz, explicação do cancelamento catastrófico, ressalva sobre cancelamento interno, a leitura "álgebra × cálculo" da prova e a discussão do custo em generalidade estão corretos e bem explicados.
