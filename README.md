# ♻️ Descarte Fácil

**O jeito certo de se desfazer das coisas, sem precisar de carro.**

Projeto CitizenTech para facilitar o descarte correto de móveis, eletrodomésticos e objetos volumosos na cidade de São Paulo.

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![ML](https://img.shields.io/badge/ML-Random%20Forest%20%7C%20XGBoost-2E8B57)](#machine-learning)
[![Dados](https://img.shields.io/badge/Dados-SP156-blue)](https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156)

## O problema

Mesmo com serviços públicos de coleta e pontos de entrega, encontrar informações sobre datas, locais e destinação correta pode ser difícil — especialmente para pessoas sem carro, idosos e quem mora sozinho.

## A solução proposta

- **Consulta por CEP:** descobrir quando o Cata-Bagulho atende a região.
- **Registro de objetos:** informar o item e seu endereço para acompanhamento.
- **Orientação de descarte:** indicar o destino adequado para cada material.
- **Reutilização:** permitir que objetos em bom estado sejam oferecidos para doação.

## Machine Learning

**Pergunta de pesquisa**

> É possível prever a quantidade de solicitações de remoção de grandes objetos por distrito e mês, utilizando o histórico público do SP156?

A tarefa é formulada como regressão: estimar o volume mensal de solicitações registradas por região.

### Dados

- Fonte: [Dados do SP156 — Portal de Dados Abertos da Prefeitura de São Paulo](https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156).
- Recorte: solicitações relacionadas à remoção de grandes objetos.
- Unidade de análise nesta execução: distrito × mês (campo geográfico disponível na extração).
- Alvo: quantidade de solicitações registradas no mês.

> **Limitação importante:** solicitações registradas não equivalem a todos os descartes irregulares que ocorrem na cidade. O modelo estima demanda registrada no SP156, não a ocorrência real de descarte em cada rua.

### Modelos e avaliação

O notebook compara uma linha de base (valor do mês anterior) com **Random Forest** e **XGBoost**, testando diferentes hiperparâmetros. O notebook foi atualizado para selecionar hiperparâmetros em uma validação temporal e reservar os meses finais para teste. As métricas finais ainda precisam ser geradas executando novamente o notebook.

A coluna de data foi conferida: valores como `07/25/2026` confirmam o padrão mês/dia/ano (MM/DD/YYYY). O notebook já foi corrigido para interpretar esse formato explicitamente. As saídas antigas foram removidas para evitar que métricas calculadas com a configuração anterior sejam confundidas com os novos resultados.

## Estrutura

```text
descarte-facil/
├── ml/
│   └── descarte_facil_ml.ipynb
├── docs/
│   └── metodologia.md
├── RELATORIO_ML.md
├── requirements.txt
├── .gitignore
└── README.md
```

## Como executar

1. Baixe a base na [página oficial do SP156](https://dados.prefeitura.sp.gov.br/dataset/dados-do-sp156).
2. Confira o dicionário e utilize os campos Data de Abertura, Serviço e Distrito presentes na extração atual.
3. Instale as dependências: `python -m pip install -r requirements.txt`.
4. Abra `ml/descarte_facil_ml.ipynb` no Jupyter ou Google Colab.
5. O notebook utiliza o CSV oficial configurado e os campos Data de Abertura, Serviço e Distrito; se a Prefeitura alterar o arquivo, confira os nomes e atualize a URL.
6. Execute as células em ordem.
7. Registre no [relatório](RELATORIO_ML.md) as métricas e a interpretação obtidas no teste.

## Entregáveis

- [Notebook de Machine Learning](ml/descarte_facil_ml.ipynb)
- [Relatório de Machine Learning](RELATORIO_ML.md)
- [Metodologia e limitações](docs/metodologia.md)

## Limitações e cuidados

- O SP156 representa solicitações registradas e está sujeito à subnotificação.
- Mais de uma solicitação pode se referir a um mesmo problema.
- A coluna geográfica utilizada nesta execução é Distrito, não Subprefeitura.
- Mudanças na classificação dos serviços podem afetar a série histórica.
- A agregação por distrito não permite afirmar a demanda exata de uma rua ou CEP.
- Previsões não substituem o calendário oficial nem confirmam atendimento.

## Impacto esperado

A análise pode apoiar a identificação de regiões e períodos com maior demanda registrada, ajudando a orientar comunicação pública e planejamento. Para o cidadão, a proposta é tornar a informação sobre descarte correto mais simples e acessível.

---

**Descarte Fácil — o jeito certo de se desfazer das coisas, sem precisar de carro.**
