# **Estratégia de Cupons como Alavanca de Crescimento no iFood**

![image](https://github.com/user-attachments/assets/f8083a6d-3862-408e-b520-56116c78591d)

## Execução dos códigos
Para executar os códigos do notebook (Case iFood completo.ipynb), abra o arquivo no Google Colab e execute as células sequencialmente.
Todas as bibliotecas já foram importadas e não há a necessidade de realizar a instalação de nenhuma dependência

## Introdução
Este relatório apresenta a análise de viabilidade financeira de uma campanha de cupons testada via experimento A/B no iFood, além de recomendações para otimizar a iniciativa. Essa análise tem como objetivo demonstrar o impacto financeiro da campanha, o tempo necessário para atingir lucro e propor melhorias para maximizar o retorno sobre o investimento (ROI). A análise considera métodos estaísticos para avaliar se as mudanças foram ao acaso ou estatísticamente relevantes, além de apresentar cenários com e sem outliers (dados cujo os valores não representam o comportamento médio) para garantir robustez.

## Premissas
Hipótese Nula: Ambos os grupos são iguais e o cupom não gerou impacto

Hipótese Alternativa: O cupom gerou impacto e ambos os grupos são diferentes

Para fins da analise irei assumir que o cupom foi no valor de R\$10,00.

Metodologia: Uso de Pyspark e métodos estatísticos como Teste U (Mann-whitney) (Não paramétrico), Teste Z de proporção e Shapiro-Wilk. Uma alternativa seria normalizar os dados e usar o Test T, porém o Teste U é mais robusto a outliers e dados não normais.

Cenários: Análise com e sem outliers.

**Outliers**

Valores extremos que fogem do comportamento médio, como usuários com gastos ou pedidos muito acima da média. Avaliar ambos os cenários é crucial porque eles podem distorcer a análise, mas também refletem usuários reais de alto valor. Removê-los completamente pode subestimar o impacto da campanha e segmentos importantes enquanto mantê-los sem análise separada pode superestimar resultados gerais, comprometendo a representatividade.


### **1.	Avaliação do Teste A/B**

#### **A)	Indicadores Relevantes e Impacto Significativo**

**Indicadores:**

- Receita por usuário: Média com outliers (Target `R$151,90` reais, Controle `R$134,33`), média sem outliers (Target `95,00`, Controle `86,00`).

- Taxa de retenção: Target 57,62%, Controle 47,63%, Lift de 20,98%.

- Quantidade de pedidos por usuário: Com outliers (Target 3,17, Controle 2,80), sem outliers (Target 2,11, Controle 1,90).

- Ticket médio por usuário: Com outliers (Target `R$47,83`, Controle `R$48,00`), sem outliers (Target `R$42,48`, Controle `R$42,23`).

- Volume de itens por pedido: Com outliers (Target 7,44, Controle 6,50), sem outliers (Target 4,20, Controle 3,80).

- Latência entre pedidos: Sem diferença signifcativa entre os grupos

**Impacto:**

- Receita incremental: 13,07% com outliers, 10,47% sem outliers

- Retenção aumentou significativamente (lift de 20,98% validado estatísticamente).

- Aumento em pedidos e volume de itens, com ticket médio estável.

#### **B)	Viabilidade Financeira**

**Resultados:**

- Visualizando a evolução dos pedidos ao longo do tempo, para apoiar a confirmação da hipótese alternativa:
  ![image](https://github.com/user-attachments/assets/25e4d172-f300-4a57-8899-46931d198e04)

![image](https://github.com/user-attachments/assets/181c5510-2fcc-4c78-b80d-e85716ba072f)


- Receita incremental: Com outliers: `R$17,57` (Target 151,90  – Controle 134,33 ), sem outliers: `R$9,00` (Target 95,00 – Controle 86,00).

- Margem Incremental: Com outliers: `R$3,51` (20% de 17,57), sem outliers: `R$1,80`(20% de 9,00).

- Prejuízo Inicial: Com outliers: `R$6,49` (3,51 – 10,00), sem outliers: `R$8,20` (1,80 – 10,00).

- Retenção Futura: Cosiderando o lift de retenção de 20,98%, podemos projetar que 21% dos usuários do grupo Target continuem comprando. Com um ticket médio futuro baseado no Controle (`R$134,33` com outliers, `R$126,00` sem outliers), aplicamos a margem bruta de 20% resultando em `R$26,87` por pedido (20% de `R$134,33`). Cada usuário retido pode gerar `R$80,61` em 3 pedidos futuros (`R$26,87 x 3`). Ponderando pelo Lift (`R$80,61` x 21% = `R$16,93`), o lucro líquido por usuário é de `R$10,44` (com outliers: `R$16,93` – `R$6,49`) e `R$8,13` (sem outliers: `R$16,93` – `R$8,20`).

- Break-even: aproximadamente 1,15 pedidos (com outliers) e 1,5 pedidos (sem outliers)

**Conclusão:** Viável a médio prazo, com recuperação do investimento impulsionada pela retenção.

#### **C)	Oportunidades de melhoria e novo teste A/B**

Oportunidades:
1.	Reduzir latência com cupons de validade curta.
2.	Minimizar perda de 42,38% dos usuários com segmentação.
3.	Melhorar segmentação por frequência e ticket médio.
4.	Aumentar ticket médio com mínimos de pedido.

**Proposta de Teste:**

**Hipótese:**

- Personalização por segmento aumenta retenção e reduz custo por conversão.

**Grupos:**

- Controle: Sem cupons.
- Target A: Cupons progressivos (Ex.: `R$5,00` no 1º pedido, `R$10,00` no 2º, `R$15,00` no 3º) para usuários de alta frequência (Média > 3 pedidos/mês).
- Target B: Cupons de validade curta (ex.: 48h) com valor fixo de `R$7,00` para usuários de baixa frequência (média <=1 pedido/mês).

**Métricas:**
- Primária: Taxa de retenção após 30 dias.
- Secundárias: Receita incremental, quantidade de pedidos, latência entre pedidos, custo por conversão, volume de itens por compra e ticket médio.

**Período:**
- 60 dias, com acompanhamento semanal.
 
**Segmentação:**
- Usuários de alta frequência: Identificados com base na média de pedidos no último trimestre.
- Usuários de baixa frequência: Aqueles com menos de 2 pedidos/mês

**Justificativa:**

Cupons progressivos incentivam a repetição de pedidos em usuários já engajados, maximizando o Lift de retenção.

Cupons de validade curta criam urgência para usuários menos frequentes, reduzindo a latência.

**Impacto Esperado:**
- Retenção: Aumento de 5-10% no Lift de retenção.
- Custo: Redução do custo médio por conversão em 20-30% devido a cupons segmentados.
- Receita: Incremento adicional de `R$5-10` por usuário devido a maior frequência de pedidos.

**Conclusão:**

A campanha de cupons demonstrou ser uma alavanca eficaz de crescimento, com aumento de 13,07% na receita (com outliers) e Lift de 20,98% na retenção. Apesar do prejuízo inicial, o investimento é recuperado com aproximadamente 1,5 pedidos futuros (por usuário retido no Lift).

**Recomendações:**
1.	Escalonar a campanha com ajustes no valor e validade dos cupons para otimizar o ROI.

2.	Implementar o novo teste A/B com cupons segmentados para validar a personalização.

3.	Monitorar métricas em tempo real (retenção, latência, custo por conversão) para ajustes dinâmicos).

4.	Investir em segmentação avançada para direcionar estratégias a públicos específicos, reduzindo perdas e aumentanto engajamento.

### **2.	Segmentação de usuários**

#### **A)	Definição de Segmentações**
Segmentos baseados no teste A/B:

- Frequência de Pedidos: Baixo (≤ 3 Pedidos) e Alto (> 3 pedidos).
- Ticket Médio: Baixo (≤ `R$100,00`) e Alto (>`R$100,00`).
- Faixa de Preço do Restaurante: Econômico (price_range ≤ 3) e Premium (price_range > 3)
- Plataforma: Android, iOS, Desktop, Windows Phone, null.
- Usuários Inativos Reativados: Usuários com active = False que voltaram a realizar pedidos após o teste.

#### **B)	Critérios e Racional**
Segmentos refletem comportamentos e canais, otimizando estratégias personalizadas

**Frequência:**
- Usuários com um número de pedidos maior que 3 foram considerados “Alto”, abaixo disso “Baixo”.
Ticket Médio:
- Calculado pela media de transações, usuários acima de 50 foram considerados “Alto”, abaixo disso “Baixo”.
Faixa de Preço:
- Baseado em price_range, restaurantes com a faixa acima de 3 foram considerados “Premium”, abaixo disso “Econômico”, refletindo o perfil do restaurant.
Plataforma:
- Categorização por origem do Sistema operacional do aparelho usado para o pedido, procurando avaliar se diferentes sistemas operacionais afetam a compra por conta da experiência do usuário.

**Usuários Inativos Reativados:**
- Identificados pela mudança de active = False, para atividade pós-cupom, com base na data de cadastro e do primeiro pedido.

#### **C)	Análise por Segmentos**
Foram analisados 20 combinações:

**As melhores segmentações:**
- Frequência alta de compra, Baixo Ticket, Restaurantes Premium, Plataforma Windows Phone = +13,64% (Pedidos), +11,78% (Receita), + 0,97% (Ticket Médio).
Piores segmentações:
- Frequência alta de compra, Alto Ticket, Restaurantes Econômicos, Plataforma Windows Phone = -16,66% (Pedidos), -13,39% (Receita), +4,31% (Ticket Médio)
Segmentações Intermediárias:
- Baixa frequência de compra, Baixo Ticket, Restaurantes Econômicos, Plataforma Windows Phone = +3,29% (Pedidos), +6,31% (Receita), +2,80% (Ticket Médio)
- Baixa frequência de compra, Alto Ticket, Restaurantes Premium, Plataforma Android = +2,43% (Pedidos), -2,69% (Receita), -3,73% (Ticket Médio).
- Baixa frequência de compra, Baixo Ticket, Restaurantes Econômicos, Plataforma Windows Phone = +2,29% (Pedidos), +2,84% (Receita), +0,31% (Ticket Médio).
- Baixa frequência de compra, Baixo Ticket, Restaurantes Premium, Plataforma Windows Phone = +5,71% (Pedidos), +4,41% (Receita), -1,12% (Ticket Médio).

**Reativação Inativos:**
- Lift de +58,38% (Pedidos), +55,18% (Receita, de `R$114.324,95` para `R$117.405,22`)

**Tendências:**
- Segmentação Baixa Frequência: Os segmentos de baixa frequência (≤ 3 pedidos) apresentam lifts positivos moderados em gasto, variando entre 2% e 6% (ex.: +6,31% em Baixo Frequência, Baixo Ticket, Econômico, Plataforma Windows Phone). Isso indica que usuários menos ativos respondem bem aos cupons, aumentando tanto os pedidos (+3,29%) quanto o ticket médio (+2,80%), sugerindo uma oportunidade de reengajamento para essa base.
- Segmentação Frequência Alta de Compra/Alto Ticket (quedas até -16,66%): Segmentos com alta frequência (> 3 pedidos) e alto ticket (>`R$100`) mostram quedas significativas, como -13,39% em gasto e -16,66% em pedidos (ex.: Frequência alta de compra, Alto Ticket, Econômico, Plataforma Windows Phone). Essas quedas podem decorrer de saturação, onde usuários frequentes e de alto valor já estão engajados e rejeitam cupons, ou de uma percepção de desvalorização, reduzindo sua atividade. Isso destaca a necessidade de estratégias alternativas, como fidelidade, para esse grupo.
- Usuários Inativos: O segmento "Reativação Inativos" refere-se a usuários previamente inativos (active = false) que retomaram a atividade após receberem cupons. O lift de +58,38% em pedidos indica um aumento significativo na quantidade média de pedidos por usuário reativado em comparação com o período de inatividade ou com um grupo controle de inativos sem intervenção, o aumento desses pedidos indicam que os cupons reengajaram usuários inativos, elevando significativamente sua atividade e despesa. Esse resultado sugere um alto potencial de recuperação.

### **3.	Próximos Passos**

**Recomendações e Impacto:**
**1.	Escalonar campanha em baixa frequência:**
- Ação: Implementar cupons de `R$5` com validade de 48 horas direcionados a usuários de baixa frequência (≤ 3 pedidos/mês) e baixo ticket médio (≤ `R$100`) em restaurantes econômicos (price_range ≤ 3 ou average_ticket ≤ `R$50`).
- Impacto:  Implementar cupons de `R$5` com validade de 48 horas direcionados a usuários de baixa frequência (≤ 3 pedidos/mês) e baixo ticket médio (≤ `R$100`) em restaurantes econômicos (price_range ≤ 3 ou average_ticket ≤ `R$50`).

- Motivo: Usuários de baixa frequência responderam bem aos cupons (lifts de 2-6%), indicando que incentivos acessíveis podem estimular novos pedidos sem saturar o mercado.

**2.	Explorar Plataforma Windows Phone**
- Ação: Oferecer cupons para usuários de alta frequência (> 3 pedidos/mês) com baixo ticket médio (≤ `R$100`) em restaurantes premium, focando na plataforma Windows Phone.
- Impacto: O lift de +11,78% reflete um aumento de `R$22,57` por usuário (de `R$191,49` no controle para `R$214,06` no target). Com cupons de `R$3` e média de 3 a 5 pedidos por usuário (baseado em 5,68 pedidos no segmento), o custo do cupom varia de `R$9` a `R$15`. Essa variação ocorre porque o cupom é aplicado por pedido (`R$3` cada), e usuários de alta frequência podem realizar entre 3 pedidos (mínimo para alta frequência, custo de `R$3` x 3 = `R$9`) e 5 pedidos (arredondado da média de 5,68 no target, custo de `R$3` x 5 = `R$15`). Subtraindo o custo, o ganho líquido é de `R$13,57` (para 3 pedidos) a `R$7,57` (para 5 pedidos). Ajustando para uma margem conservadora e possíveis variações, estima-se um ganho médio de `R$11` a `R$13` por usuário, considerando também o impacto de retenção futura e margem de 20% (como no item 1b).
- Motivo: O sucesso desse segmento (+11,78% em gasto) sugere que a plataforma Windows Phone pode ser subutilizada, oferecendo uma oportunidade de expansão com custos baixos por cupom.

**3.	Maximizar Reativação de Inativos**
- Ação: Distribuir cupons de `R$5` com validade de 72 horas para usuários inativos (active = false), acompanhados de notificações push para incentivar o retorno.
- Impacto: Com base no lift de +55,18% em gasto (de `R$114.324,95` para `R$177.405,22`), espera-se um ganho de `R$55` por usuário reativado, além de um aumento potencial de 50% na base ativa, aproveitando o alto engajamento observado nesse grupo.
- Motivo: A reativação de inativos demonstrou um impacto excepcional (+58,38% em pedidos, +55,18% em gasto), indicando que incentivos temporários com notificação direta podem recuperar uma parcela significativa de usuários perdidos.

**4.	Redirecionar Frequência Alta de Compra/Alto Ticket:**
- Ação: Suspender a oferta de cupons para usuários de alta frequência (> 3 pedidos/mês) e alto ticket (>`R$100`), substituindo por um programa de fidelidade, como cashback ou pontos acumulados.
- Impacto: Essa mudança visa evitar perdas de `R$13` a `R$50` por usuário, refletindo as quedas observadas (ex.: -13,39% em gasto e -16,66% em pedidos no segmento Frequência alta de compra, Alto Ticket, Econômico, Plataforma Windows Phone), preservando a receita desse grupo valioso.
- Racional para a Faixa de Perdas: A queda de -13,39% em gasto nesse segmento representa uma redução de `R$55,90` por usuário (de `R$417,49` no controle para `R$361,59` no target). Considerando a margem de lucro de 20% (item 1b), a perda de margem é de `R$11,18` (20% de `R$55,90`), ajustada para `R$13` para simplificação e considerando impactos de retenção futura. O limite superior de `R$50` aproxima-se da perda bruta (`R$55,90`, arredondado), refletindo cenários onde a redução no gasto não é compensada (ex.: sem retenção futura). A faixa de `R$13` a `R$50` captura a perda líquida ajustada (considerando margem e custos de cupons) e a perda bruta máxima, refletindo a variabilidade no comportamento desses usuários.
- Motivo: As quedas significativas sugerem saturação ou rejeição a cupons por esses usuários, tornando alternativas como fidelidade mais adequadas para manter seu engajamento.

**5.	Proteger Ticket Médio:**
- Ação: Estabelecer um mínimo de `R$100` para a utilização de cupons de `R$10`, direcionado a usuários de alto ticket (>`R$100`).
- Impacto: Prevê-se um aumento de `R$3` a `R$5` por pedido, protegendo as margens e contrabalançando quedas no ticket médio observadas em alguns segmentos (ex.: -3,73% em Baixo Frequência, Alto Ticket, Restaurantes Premium, Android).
- Motivo: A estabilidade do ticket médio no teste geral (ex.: Controle `R$48,00` vs. Target `R$47,83` com outliers) pode ser ameaçada por cupons sem restrições; o mínimo incentiva compras maiores, alinhando-se à estratégia de lucratividade.

**6.	Realizar o novo Teste A/B sugerido**

**Riscos das ações propotas:**
1.	Baixa Adesão em Segmentos Saturos: Frequência alta de compra/alto ticket pode rejeitar cupons ou fidelidade, ampliando perdas (-16,66% pedidos em alguns casos).

2.	Custo Elevado de Reativação: Campanhas para inativos podem não compensar se a retenção futura for inferior ao lift esperado (+55,18%), aumentando o prejuízo inicial.

3.	Fragmentação do Ticket Médio: Mínimos de `R$100` podem reduzir uso de cupons em alto ticket.

4.	Dependência de Dados: Erros em dados nulos ou falhas na segmentação podem levar a decisões imprecisas, afetando o ROI.

5.	Resistência Operacional: Implementação em Plataforma Windows Phone pode enfrentar limitações técnicas ou baixa base de usuários.

### **Conclusão:**
A campanha de cupons é viável, aumentando a receita em 13,07% e a retenção em 20,98%. Ela se destaca em baixa frequência (lift de 6,31%) e na reativação de inativos (lift de 55,18%), mas requer ajustes para usuários de frequência alta de compra e alto ticket, que apresentaram quedas de até 16,66%. As ações propostas visam ganhos de `R$10` a `R$55` por usuário e um aumento na retenção de 25-30%, com riscos mitigados por monitoramento contínuo e um novo teste A/B. Recomendo a aprovação e implementação
