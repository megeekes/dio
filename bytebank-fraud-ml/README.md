# ByteBank — Detecção de fraudes em transações com Machine Learning

## Por que desenvolvi este projeto

A detecção de fraudes em transações financeiras é um problema que conecta análise de dados, segurança da informação e gestão de riscos. Neste desafio, quis explorar como diferentes algoritmos de Machine Learning podem apoiar a identificação de operações suspeitas — e, principalmente, entender os limites dessas previsões.

Minha experiência com Cybersecurity influenciou a forma de olhar para o problema: detectar ameaças é importante, mas também é preciso considerar falsos alertas, rastreabilidade das decisões e impactos para quem utiliza o serviço. Um modelo pode apresentar acurácia elevada e ainda assim falhar no objetivo central quando os casos fraudulentos são raros.

Este é um projeto educacional, não uma solução pronta para bloquear transações em produção.

## Objetivo e abordagem

O notebook está organizado em cinco etapas:

1. **Exploração dos dados:** conhecer o dataset e verificar a proporção entre transações legítimas e fraudulentas.
2. **Preparação:** separar os conjuntos de treino, validação e teste, com cuidado para evitar vazamento de informações.
3. **Modelagem:** treinar e comparar Regressão Logística, Random Forest e XGBoost.
4. **Avaliação:** observar recall, precision, F1 e Average Precision (AP), além de ajustar o limiar de classificação na validação.
5. **Explicabilidade:** utilizar SHAP para investigar quais variáveis mais influenciam as previsões do modelo selecionado.

A ideia não é escolher um algoritmo apenas porque ele apresentou o maior número em uma métrica. Em um cenário de fraude, um falso negativo pode deixar passar uma transação indevida; por outro lado, falsos positivos em excesso podem gerar bloqueios ou revisões desnecessárias. Por isso, o recall da classe fraudulenta é uma prioridade, mas deve ser analisado junto com a precisão e o volume de alertas.

## Dataset

O projeto utiliza o conjunto público **Credit Card Fraud Detection**, com 284.807 transações e 492 casos de fraude, registrados em setembro de 2013. A proporção muito pequena de fraudes torna a acurácia isolada pouco informativa.

- [Dataset original no Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- [Espelho utilizado para download automático](https://huggingface.co/datasets/David-Egea/Creditcard-fraud-detection/blob/main/creditcard.csv)
- [Referência alternativa no Zenodo](https://zenodo.org/records/7395559)

O notebook prevê download automático do CSV (aproximadamente 151 MB) e verificação de integridade por SHA-256. Se o servidor estiver indisponível, é possível colocar manualmente o arquivo `creditcard.csv` na pasta `data/`. O CSV não deve ser incluído no repositório.

## Como executar no Windows

No PowerShell, dentro da pasta do projeto:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter lab
```

Abra `notebooks/deteccao_fraudes.ipynb` e execute as células na sequência. O notebook gera os arquivos de avaliação na pasta `results/`.

## Decisões técnicas

A separação dos dados foi definida como **60% treino, 20% validação e 20% teste**, de forma aleatória e estratificada, com `random_state=42`. Na Regressão Logística, a padronização é aplicada por um `Pipeline`, ajustado somente com os dados de treino.

Para lidar com o desbalanceamento, a implementação utiliza pesos de classe em vez de aplicar reamostragem antes da divisão. Os três modelos são comparados na validação por métricas apropriadas para eventos raros.

O limiar de decisão não é tratado como um valor fixo e universal. O notebook testa uma regra **ilustrativa**: maximizar o recall com precision mínima de 20% na validação. Caso nenhum modelo cumpra essa condição, há uma alternativa baseada em F1, explicitamente sinalizada. Esse limite de 20% não é uma exigência bancária nem uma conclusão sobre o custo real de investigar alertas.

O conjunto de teste fica reservado para a avaliação final, depois da escolha do modelo e do limiar. Para a análise de explicabilidade, o SHAP é aplicado a uma amostra de 100 transações.

## Considerações de Cybersecurity

Mesmo com bom desempenho estatístico, um detector de fraude precisa ser analisado como parte de um processo de segurança maior:

| Risco ou preocupação | Cuidados necessários |
| --- | --- |
| Fraudes não identificadas | Acompanhar falsos negativos e mudanças no padrão das transações |
| Alertas indevidos | Medir falsos positivos e prever revisão humana quando apropriado |
| Dados incorretos ou manipulados | Validar a origem, a integridade e a qualidade dos dados |
| Mudança no comportamento das fraudes | Monitorar degradação de desempenho e reavaliar o modelo periodicamente |
| Falta de rastreabilidade | Registrar versões, parâmetros, métricas e decisões de forma auditável |
| Exposição de informações financeiras | Aplicar minimização de dados e controle de acesso |

Essas medidas representam considerações para uma eventual aplicação real; **não são controles implementados e validados neste notebook**.

## Resultados e análise crítica

**Status atual:** a estrutura do experimento foi preparada, mas os modelos ainda precisam ser executados para produzir resultados verificáveis. As métricas, os gráficos e a escolha final do modelo devem ser registrados somente após a execução completa.

Após os testes, pretendo documentar:

- O recall e a precision de cada modelo na validação.
- A diferença entre o limiar padrão de 0,50 e o limiar escolhido.
- A quantidade de falsos positivos e falsos negativos no teste.
- O que os resultados do SHAP permitem — e não permitem — interpretar.
- Qual modelo eu escolheria para o cenário estudado e quais compromissos essa decisão envolve.

## Limitações

O dataset é histórico e anonimizado. As variáveis `V1` a `V28` são componentes transformados por PCA, o que limita sua interpretação de negócio. Não há identificadores de cliente, custos operacionais dos alertas ou uma avaliação temporal rigorosa nesta implementação.

A divisão aleatória estratificada é adequada para o exercício, mas não substitui testes cronológicos e validações operacionais necessários em um ambiente financeiro real. As explicações do SHAP indicam influência nas previsões, não relações de causa e efeito.

## Próximos passos

Depois de executar e revisar os resultados, os próximos aprimoramentos seriam testar diferentes restrições de precision, estudar a calibração das probabilidades, incorporar custos de investigação e explorar uma estratégia de validação temporal.

O principal aprendizado que busco com este projeto é avaliar modelos não apenas pelo desempenho matemático, mas também pela utilidade, pelas limitações e pelos riscos associados às decisões que eles podem apoiar.
