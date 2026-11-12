# ByteBank — Detecção de fraudes com Machine Learning

Projeto educacional em cinco etapas: exploração, preparação, comparação de Regressão Logística / Random Forest / XGBoost, ajuste de limiar e explicabilidade SHAP.

## Executar no Windows (PowerShell)

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter lab
```

Abra `notebooks/deteccao_fraudes.ipynb` e execute as células em ordem.

**Dados:** download automático (~151 MB) a partir de https://huggingface.co/datasets/David-Egea/Creditcard-fraud-detection/blob/main/creditcard.csv, com verificação SHA-256. Dataset original: https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud. Espelho independente: https://zenodo.org/records/7395559.

**Aviso:** o download depende da disponibilidade do servidor e da ligação à internet. Caso falhe, descarregue manualmente `creditcard.csv` para `data/`. Não inclua o CSV no GitHub.

## Metodologia

- Divisão aleatória estratificada 60/20/20, seed 42.
- `StandardScaler` dentro de pipeline na regressão logística (ajuste apenas no treino).
- Pesos de classe em vez de reamostragem antes da divisão.
- AP (Average Precision), recall, precision e F1.
- Limiar selecionado na validação com precision mínima ilustrativa de 20%; fallback de F1 quando inviável.
- Teste usado uma única vez após a seleção.
- SHAP em amostra de 100 transações para interpretação aproximada.

## Resultados

Após executar o notebook, consulte os ficheiros gerados em `results/`. Os resultados **não foram executados nem inventados** neste repositório inicial. Preencha aqui uma tabela com métricas reais antes de publicar uma conclusão.

## Limitações

Dataset de setembro de 2013, 284.807 transações, 492 fraudes. Sem identificadores de cliente, validação temporal rigorosa ou informação de custo por alerta. Variáveis PCA anonimizadas restringem interpretação. Não é um sistema de decisão bancária em produção.

## Publicação

```powershell
git init
git add .
git commit -m "Projeto de detecção de fraude com ML"
git branch -M main
git remote add origin https://github.com/SEU_USUARIO/bytebank-fraud-ml.git
git push -u origin main
```

Crie antes um repositório vazio com esse nome na sua conta do GitHub. Não inclua o dataset.
