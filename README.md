# Análise das Expectativas Econômicas - Boletim Focus

Projeto para análise e visualização das expectativas de mercado divulgadas pelo Banco Central do Brasil através do Boletim Focus.
A utilização das bibliotecas `pandas`, `python-bcb` e `matplotlib`.

## 📊 Sobre o Projeto

Este projeto visa automatizar a coleta, análise e visualização das expectativas de mercado divulgadas semanalmente pelo Banco Central do Brasil através do Boletim Focus. Focado nos principais indicadores econômicos, oferece insights atualizados sobre as projeções de instituições financeiras para o ano de 2025.
- **IPCA**: Indíce de Preços do Consumidor Amplo.
- **Taxa Selic**: Taxa de juros básica da economia.
- **Câmbio (R$/US$)**: Taxa de juros básica da economia.

### EXTRAÇÃO DOS DADOS E CRIAÇÃO DO GRÁFICO
```bash

!pip install python-bcb
from bcb import Expectativas
import pandas as pd
import matplotlib.pyplot as plt   ## Extraindo dados com 'Expectativas'

exp = Expectativas()           # Para ver quais são os tipos de bases de expectativas
exp.describe()

exp = exp.get_endpoint('ExpectativasMercadoAnuais')

## FILTRAGEM DAS COLUNAS QUE VAMOS UTILIZAR!
ipca_expec = (exp.query()
  .filter(exp.Indicador == 'IPCA', exp.DataReferencia == 2025)              
  .filter(exp.Data >= '2024-01-01')                                        
  .filter(exp.baseCalculo == '0')
  .select(exp.Indicador, exp.Data, exp.Media, exp.Mediana, exp.DataReferencia) 
  .collect()                                                              
   )
ipca_expec['Data'] = pd.to_datetime(ipca_expec['Data'], format = '%Y-%m-%d')


selic_expec = (exp.query()
  .filter(exp.Indicador == 'Selic', exp.DataReferencia == 2025) 
  .filter(exp.Data >= '2024-01-01')                                       
  .filter(exp.baseCalculo == '0')
  .select(exp.Indicador, exp.Data, exp.Media, exp.Mediana, exp.DataReferencia) 
  .collect()                                                              
   )
selic_expec['Data'] = pd.to_datetime(selic_expec['Data'], format = '%Y-%m-%d')


cambio_expec = (exp.query()
  .filter(exp.Indicador == 'Câmbio', exp.DataReferencia == 2025)             
  .filter(exp.Data >= '2024-01-01')                                        
  .filter(exp.baseCalculo == '0')
  .select(exp.Indicador, exp.Data, exp.Media, exp.Mediana, exp.DataReferencia) 
  .collect()                                                              
   )
cambio_expec['Data'] = pd.to_datetime(cambio_expec['Data'], format = '%Y-%m-%d')


plt.figure(figsize = [9,4])
plt.plot(cambio_expec['Data'], cambio_expec['Media'], color='green', label='Taxa de Câmbio')  # Plot 'Media' in green
plt.plot(ipca_expec['Data'], ipca_expec['Media'], color='red', label='Taxa de IPCA')
plt.plot(selic_expec['Data'], selic_expec['Media'], color='blue', label='Taxa Selic')
plt.ylabel('Taxa (%)', fontsize = 10)
plt.xlabel('Mediana')
plt.title('Expectativa Boletim Focus')
plt.legend()
plt.grid(axis = 'y', linestyle = '--')
plt.show()
