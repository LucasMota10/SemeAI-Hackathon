# 🌱 Agro-ML — Recomendação de Culturas para Agricultura Familiar na Bahia

Sistema de recomendação de plantas baseado em previsão climática e análise de solo, desenvolvido para apoiar agricultores familiares do estado da Bahia.

---

## 💡 Ideia do Projeto

O agricultor familiar muitas vezes toma decisões de plantio baseadas em experiência empírica ou tradição, sem acesso fácil a dados climáticos e agronômicos integrados. O **Agro-ML** resolve isso em três etapas simples:

1. **O usuário informa uma coordenada geográfica** (latitude e longitude do seu terreno)
2. **O sistema prevê o clima dos próximos 30 dias** para aquela localização, usando dados históricos de estações meteorológicas próximas
3. **A previsão climática é cruzada com dados de solo e tolerâncias agronômicas** de centenas de espécies, gerando um ranking das plantas com maior compatibilidade para aquele local e momento

O foco são culturas de ciclo curto viáveis para a agricultura familiar baiana: milho, feijão, mandioca, cacau, soja, banana, mamão, caju, café, sorgo e outras.

---

## 🔄 Pipeline

```
Coordenada (lat, lon)
        │
        ▼
Estações meteorológicas mais próximas
        │
        ▼
Série histórica de clima (horário → diário)
        │
        ▼
┌───────────────────────────────┐
│  Previsão climática 30 dias   │
│  ├── Prophet                  │
│  ├── XGBoost (features de lag)│
│  └── LSTM (janela anual)      │
│  → Melhor modelo por RMSE     │
└───────────────────────────────┘
        │
        ▼
Condições previstas (temp, chuva, umidade)
  +
Perfil do solo local (pH, argila, drenagem...)
        │
        ▼
Score de adequação por espécie vegetal
  (faixa ótima × faixa absoluta × bônus cultura-alvo)
        │
        ▼
Top 5 plantas recomendadas + dashboard visual
```

---

## 📁 Datasets

Os datasets necessários para rodar o projeto estão disponíveis no Google Drive:

📂 **[Acessar datasets no Google Drive](https://drive.google.com/drive/folders/1mHHd9p4Po87JC5pTBXjX4YVcHFNTNnrd?usp=drive_link)**

Baixe os arquivos e coloque-os na pasta `data/` do projeto com os seguintes nomes:

| Arquivo | Descrição |
|---|---|
| `data/dataset_clima.csv` | Dados horários de estações meteorológicas do INMET |
| `data/dataset_estacoes.csv` | Cadastro de estações com coordenadas geográficas |
| `data/dataset_plantas.csv` | Tolerâncias agronômicas por espécie (baseado no ECOCROP/FAO) |
| `data/dataset_solo.csv` | Características físico-químicas do solo por UF |

---

## ⚙️ Instalação

```bash
pip install prophet xgboost scikit-learn matplotlib seaborn tensorflow
```

---

## 🚀 Como usar

1. Abra o notebook `main.ipynb`
2. Na **Célula 2**, configure as coordenadas do local desejado:

```python
USER_LAT = -12.9714   # latitude
USER_LON = -38.5014   # longitude
```

3. Execute todas as células em ordem (`Run All`)
4. O resultado final é um dashboard com:
   - Previsão de temperatura e precipitação para os próximos 30 dias
   - Top 5 plantas recomendadas com score de adequação
   - Comparativo de desempenho dos modelos (RMSE)
   - Diagnóstico agronômico do solo local

Os resultados também são exportados como arquivos CSV e PNG na pasta do projeto.

---

## 🎯 Culturas-alvo monitoradas

O sistema dá prioridade às seguintes culturas típicas da agricultura familiar baiana:

| Espécie | Nome popular |
|---|---|
| *Zea mays* | Milho |
| *Phaseolus vulgaris* | Feijão |
| *Manihot esculenta* | Mandioca |
| *Theobroma cacao* | Cacau |
| *Glycine max* | Soja |
| *Vigna unguiculata* | Feijão-caupi |
| *Musa acuminata* | Banana |
| *Carica papaya* | Mamão |
| *Anacardium occidentale* | Caju |
| *Coffea arabica* | Café |
| *Sorghum bicolor* | Sorgo |
| *Ipomoea batatas* | Batata-doce |

---

## 📊 Modelos de previsão climática

Três modelos são treinados e comparados automaticamente para cada variável climática:

| Modelo | Abordagem |
|---|---|
| **Prophet** | Decomposição de séries temporais com sazonalidade anual e semanal |
| **XGBoost** | Regressão com features de lag, rolling mean e sazonalidade cíclica |
| **LSTM** | Rede neural recorrente com janela de 365 dias |

O modelo com menor RMSE médio é selecionado automaticamente para gerar a previsão final.

---

## 📍 Coordenadas sugeridas para teste na Bahia

| Região | Cidade | Latitude | Longitude |
|---|---|---|---|
| Sul da Bahia (cacau) | Ilhéus | -14.7892 | -39.0329 |
| Sudoeste baiano | Vitória da Conquista | -14.8619 | -40.8444 |
| Chapada Diamantina | Lençóis | -12.5628 | -41.3897 |
| Oeste baiano (soja/milho) | Luís Eduardo Magalhães | -12.0964 | -45.7897 |
| Semiárido | Juazeiro | -9.4119 | -40.5022 |

---

## 📤 Arquivos de saída

Após a execução completa, o notebook gera:

- `previsao_clima_30d.csv` — previsão diária das variáveis climáticas
- `top_plantas_recomendadas.csv` — ranking completo de plantas com scores
- `metricas_modelos.csv` — RMSE, MAE e MAPE de cada modelo
- `dashboard_agro_ml.png` — painel visual com todos os resultados
- `historico_clima.png` — série histórica de temperatura, chuva e umidade
- `previsao_clima.png` — gráfico da previsão dos próximos 30 dias
- `comparacao_modelos.png` — comparativo de RMSE entre modelos
