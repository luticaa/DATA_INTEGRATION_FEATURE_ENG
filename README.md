# DATA_INTEGRATION_FEATURE_ENG

Trabalho da disciplina **Integração e preparação de dados** (UEL). Estima a probabilidade de um acidente de trânsito resultar em óbito com base em variáveis espaciais, temporais e ambientais, usando o data warehouse Datatran da primeira atividade.

**Alunos:** Lucas Antonio Cunha Rodrigues da Silva / Marcos Beregula  
**Professor:** Daniel dos Santos Kaster

## Objetivo

Classificação binária (`target_obito`: 0 = sem óbito, 1 = com óbito) com **LightGBM**, a partir de dados integrados no schema `dw` (Supabase/PostgreSQL).

## Estrutura

```
timeLineDatatran/
├── timelinedatatran.ipynb   # notebook principal
├── requirements.txt
├── timelinedatatran.csv     # export bruto do DW (não versionado)
└── timelinedatatranClean.csv # dataset tratado no OpenRefine (não versionado)
```

## Ambiente

Python 3.10. Exemplo com conda:

```bash
conda create -n timelinedatatran -c conda-forge python=3.10
conda activate timelinedatatran
pip install -r requirements.txt
python -m ipykernel install --user --name timelinedatatran --display-name "Python (timelinedatatran)"
```

### macOS (Apple Silicon) — LightGBM

Se `import lightgbm` falhar por `libomp.dylib`:

```bash
brew install libomp
```

Reinicie o kernel do notebook após instalar.

Importe **scikit-learn antes** do LightGBM (já está assim no notebook). Reinicie o kernel se instalar pacotes com o notebook aberto.

## Dados

Os CSVs (~110 MB cada) **não entram no Git** (`.gitignore`).

| Arquivo | Origem |
|---------|--------|
| `timelinedatatran.csv` | Query SQL no DW (`dw.fato_acidentes_datatran` + dimensões) |
| `timelinedatatranClean.csv` | Tratamento no OpenRefine + colunas derivadas (`target_obito`, `feriado`, etc.) |

Para rodar o fluxo completo, é preciso ter os CSVs na pasta do projeto ou executar a célula de extração SQL (requer credenciais do Supabase).

## Fluxo do notebook

1. Imports e conexão com PostgreSQL (opcional se já existir CSV).
2. Leitura de `timelinedatatranClean.csv`.
3. Engenharia de atributos e seleção de features.
4. Split temporal: treino (2018–2022), teste (2023).
5. `LGBMClassifier` com `scale_pos_weight` para classe desbalanceada (~7% óbitos).
6. Métricas: acurácia, ROC-AUC, classification report, matriz de confusão.

## Dependências

Ver `requirements.txt`. Principais: pandas, psycopg2-binary, SQLAlchemy, scikit-learn, lightgbm, jupyter.
