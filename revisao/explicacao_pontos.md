# Explicação dos pontos da revisão

Cada ponto segue o mesmo formato: o que o relatório diz, por que está impreciso e como fica corrigido. Os pontos estão em ordem de importância. A lista curta continua em [revisao_teoria.md](revisao_teoria.md).

---

## 1. "Para obter f'' é preciso subtrair" (Seções 4 e 5)

**O que o relatório diz.** A parte real de f(x+ih) é f(x) − (h²/2) f''(x) + …. Para tirar f'' dela, é preciso subtrair f(x), e essa subtração traz de volta o cancelamento. Daí a conclusão: "um único i só isola uma derivada".

**Por que está impreciso.** O raciocínio está certo para o passo **puramente imaginário** (x + ih), mas a conclusão vai além do que foi mostrado. O passo complexo não precisa apontar na direção de i. Ele pode ser inclinado, com ω = e^{iπ/4} (45° no plano complexo), e então:

- ω² = i, então o termo com f'' passa para a **parte imaginária**;
- ω⁴ = −1 é real, então o termo seguinte sai da parte imaginária.

Somando as avaliações em x+ωh e x−ωh, os termos ímpares (f', f''', …) se cancelam e sobra

  Im[f(x+ωh) + f(x−ωh)] / h² = f''(x) − (h⁴/360) f⁽⁶⁾(x) + …

Aqui não aparece o f(x), que tem tamanho O(1), e portanto não há o cancelamento grave. Ainda existe um cancelamento menor: as duas partes imaginárias contêm ±h f'/√2, que se anulam. Por isso o método também **não** chega a u, mas melhora muito. Testei em f(x) = eˣ/(cos³x + sin³x), x = 1,5:

| método | melhor erro relativo em f'' |
|---|---|
| parte real, 2[f − Re f(x+ih)]/h² (relatório) | ≈ 2,6×10⁻⁸ |
| passo inclinado, ω = e^{iπ/4} | ≈ 1,9×10⁻¹² |
| dual aninhado | ≈ 5×10⁻¹⁶ (≈ u) |

**Como fica corrigido.** A tese central continua válida (só os duais aninhados chegam a u sem nenhum h). Basta restringir a frase:

> "Com passo puramente imaginário, só f' sai livre de cancelamento. Um passo inclinado, ω = e^{iπ/4}, obtém f'' sem subtrair termos de tamanho O(1) (Lai e Crassidis, 2008), mas ainda cancela termos O(h) e não chega a u. Para derivadas de ordem superior com erro u são necessárias várias unidades independentes (multicomplexos) ou duais aninhados."

Isso também rende um bom slide: mostra que a ideia do passo complexo é mais flexível do que parece, e que mesmo assim o dual vence.

---

## 2. "Ambos atingem cerca de 5×10⁻⁹" (Seção 5, Figura A3)

**O que o relatório diz.** O passo complexo (parte real) e a centrada de 2ª ordem atingem cerca de 5×10⁻⁹ em h ≈ 10⁻⁴.

**Por que está impreciso.** Perto do h ótimo, a curva de erro é **ruidosa**: o erro de arredondamento oscila de um h para o vizinho, e às vezes os erros se cancelam por acaso. O notebook informa o **mínimo** da curva (4,6×10⁻⁹ e 6,1×10⁻⁹), ou seja, o ponto mais sortudo. A própria fórmula do relatório prevê um erro relativo ótimo de

  2√(u|f||f⁽⁴⁾|/3) / |f''| ≈ 2,6×10⁻⁸,

e numa varredura o valor típico perto de h = 10⁻⁴ fica em torno de 3×10⁻⁸. O 5×10⁻⁹ é cinco vezes melhor que o previsto por sorte, não por mérito do método. Se alguém perguntar na apresentação, a teoria e o número não batem.

**Como fica corrigido.**

> "Ambos atingem erro relativo da ordem de 10⁻⁸ (previsão teórica: 2,6×10⁻⁸) em h ≈ 10⁻⁴, com mínimos pontuais de até 5×10⁻⁹ por cancelamento fortuito dos arredondamentos."

---

## 3. "Para h < u|x|, fl(x+h) = x" (Seção 2.4)

**O que o relatório diz.** Se h < u|x|, somar h a x não muda nada no computador, e a diferença finita dá exatamente zero.

**Por que está impreciso.** Somar h a x só é "engolido" quando h é menor que **meio espaçamento** entre x e o próximo número de máquina (meio ulp). Para x = 1,5, esse meio ulp é 2⁻⁵³ = u, e não 1,5u. Então, com h = 1,2u (que é menor que u|x| = 1,5u), fl(1,5 + h) ≠ 1,5. Verifiquei em Python. A ordem de grandeza do relatório está certa, só a desigualdade é estrita demais.

Há um efeito relacionado que o modelo não menciona: mesmo quando x+h não é engolido, ele é **arredondado**. O passo realmente usado é fl(x+h) − x, que difere de h. Isso dá mais um erro do tipo u|x|/h. O remédio clássico é recalcular h ← (x+h) − x antes de dividir.

**Como fica corrigido.**

> "Para h ≲ u|x| (mais precisamente, h abaixo de meio ulp de x), tem-se fl(x+h) = x e a fórmula devolve exatamente zero."

Opcionalmente, uma nota de rodapé sobre o truque h ← (x+h) − x.

---

## 4. A proposição sobre diferenças finitas (Seção 2.4)

**O que o relatório diz.** Toda fórmula de ordem p tem erro mínimo ≍ u^{p/(p+1)}, logo nenhuma chega a O(u).

**Por que está impreciso.** São três detalhes:

1. **Não é um teorema demonstrado.** O resultado vem do *modelo* de erro E(h) ≈ C h^p + K u/h, que é uma estimativa e não uma cota inferior provada. Chamar de "Proposição" sugere mais rigor do que há.
2. **Falta uma hipótese.** Se f⁽ᵖ⁺¹⁾(x) = 0 (por exemplo, f é um polinômio de grau ≤ p), o truncamento some e basta usar h grande para obter erro O(u). Exemplo: a centrada aplicada a f(x) = x² é exata para qualquer h.
3. **"Nunca atinge u" é uma afirmação sobre o expoente.** Na prática as constantes contam: neste exemplo, Richardson chega a cerca de 8×10⁻¹³, perto de u por um fator de mil. A frase continua verdadeira, mas é bom não dar a impressão de que diferenças finitas de ordem alta são ruins.

**Como fica corrigido.** Trocar "Proposição" por "Observação" (ou escrever "no modelo de erro acima"), acrescentar "supondo f⁽ᵖ⁺¹⁾(x) ≠ 0" e incluir uma frase do tipo: "com p = 4 (Richardson) já se chega a cerca de 10⁻¹² neste exemplo; o expoente nunca chega a 1, mas na prática o que limita é a constante."

---

## 5. A demonstração da proposição dos duais (Seção 4.3, Passo 1)

**O que o relatório diz.** Para φ diferenciável, a expansão de Taylor em duas variáveis com ε² = 0 dá φ(a + bε, c + dε) = φ(a,c) + [∂₁φ·b + ∂₂φ·d]ε, "pois todo termo de ordem ≥ 2 se anula".

**Por que está impreciso.** São três detalhes:

1. **Taylor com termos de ordem ≥ 2 exige mais que diferenciabilidade.** Uma função só diferenciável não tem série de Taylor. Na Seção 4.2 o relatório resolve isso corretamente, ao *definir* g(a + bε) := g(a) + g'(a)bε. O Passo 1 deveria fazer o mesmo.
2. **A indução não diz que i, k < j.** A prova supõe que os argumentos de φ_j "já valem" vᵢ + v̇ᵢε. Isso só é verdade se cada operação usa apenas variáveis calculadas **antes** dela. É óbvio para um programa, mas a indução depende disso e convém dizer.
3. **Potência com expoente variável.** Em (a + bε)^(c + dε), a derivada em relação ao expoente é a^c · log a, que exige a > 0. Vale uma nota de meia linha.

**Como fica corrigido.**

> "Cada φ_j usa apenas variáveis v_i, v_k com i, k < j. Para φ diferenciável, *definimos* φ(a + bε, c + dε) := φ(a,c) + [∂₁φ(a,c)·b + ∂₂φ(a,c)·d]ε; quando φ é analítica, isso coincide com a série de Taylor truncada, já que os termos de ordem ≥ 2 contêm ε²."

---

## 6. "A derivada exata do caminho percorrido" (Seção 4.3, laços e desvios)

**O que o relatório diz.** Com `if`/`while`, a AD calcula a derivada exata do caminho percorrido.

**Por que está impreciso.** A frase está correta, mas o leitor pode concluir que isso é sempre f'(x), e não é. A derivada do caminho só coincide com f'(x) se o caminho for o mesmo para todos os pontos perto de x. Exemplo:

```python
def f(x):
    if x == 0: return 0.0
    return x
```

Matematicamente f(x) = x, com f'(0) = 1. Em x = 0, porém, o programa segue o ramo `return 0.0`, uma constante, e a AD devolve 0.

**Como fica corrigido.** Acrescentar: "Essa derivada coincide com f'(x) desde que o mesmo caminho seja seguido em toda uma vizinhança de x; caso contrário, a AD pode errar sem avisar (exemplo: `if x == 0: return 0 else: return x` dá 0 em vez de 1)." Isso conversa bem com o resultado do `abs` em 0 do notebook.

---

## 7. "O melhor possível para qualquer método" (Seção 4.3, parágrafo Erro)

**O que o relatório diz.** O erro dos duais é O(u) vezes um fator de condicionamento, "o melhor possível para qualquer método que use aritmética de precisão u".

**Por que está impreciso.** Não há demonstração disso, e não é verdade em geral: um método pode reorganizar as contas e ter erro menor que o do código original (por exemplo, usando uma fórmula fechada mais estável para a derivada). O que se pode dizer com segurança é que o erro é da mesma ordem do erro com que se calcula a própria f.

**Como fica corrigido.**

> "…o erro final é O(u) vezes um fator de condicionamento: da mesma ordem do erro com que se calcula a própria f, o que em geral não se pode melhorar sem aumentar a precisão."

---

## 8. Conflito de notação: u (Seção 4.1)

**O que o relatório diz.** "Se a = u(x), b = u'(x), c = v(x), d = v'(x), a parte dual é uv' + u'v."

**Por que está impreciso.** No texto inteiro, u é o arredondamento unitário 2⁻⁵³. Usar u(x) como função logo depois confunde o leitor, principalmente numa seção que fala de erro.

**Como fica corrigido.** Trocar por p(x) e q(x): "a = p(x), b = p'(x), c = q(x), d = q'(x); a parte dual é pq' + p'q = (pq)'". Não use g nem h, que já têm outro papel no texto.

---

## 9. "O truncamento já é menor que u" (Seção 3)

**O que o relatório diz.** Para h ≤ √(6u|f'|/|f'''|), "o truncamento já é menor que u".

**Por que está impreciso.** A conta é h²|f'''|/6 ≤ u|f'|, ou seja, o erro **relativo** fica abaixo de u. Já o erro absoluto fica abaixo de u|f'|, que aqui é cerca de 3,6u. É só uma palavra, mas numa seção sobre erro relativo × absoluto ela importa. Pelo mesmo motivo, o "9,472×10⁻⁶" do parágrafo Truncamento é erro **absoluto** (o relativo é 2,6×10⁻⁶), e convém dizer isso.

**Como fica corrigido.** "…o erro relativo de truncamento já é menor que u" e "ambos dão erro absoluto de 9,472×10⁻⁶".

---

## 10. h = 10⁻²⁰⁰ (Seção 3, detalhe opcional)

**O que o relatório diz.** h = 10⁻³⁰ ou mesmo 10⁻²⁰⁰ são escolhas usuais.

**Por que vale uma nota.** Isso funciona para quase tudo, mas se em algum ponto do código aparecer o quadrado da parte imaginária (o módulo √(Re² + Im²), ou z·z̄), teremos (10⁻²⁰⁰)² = 10⁻⁴⁰⁰, menor que o menor double (≈ 10⁻³⁰⁸). O resultado vira zero (underflow) e a derivada some. É o mesmo mecanismo do `np.abs` que o notebook já mostra apagando a derivada.

**Como fica corrigido.** Uma nota de rodapé opcional: "h muito pequeno é seguro desde que o código não forme h², como no módulo complexo; por isso h = 10⁻²⁰ ou 10⁻³⁰ são mais prudentes que 10⁻²⁰⁰."
