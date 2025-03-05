# Lab_Azure_ML_fundamentos
<h5>No laboratório em questão, utilizaremos este repositório para descrever o processo de criação de um modelo de previsão com seus devidos pontos de extremidade configurados.
</h5>

<h4>Links úteis:</h4> 

https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/01-machine-learning.html

https://microsoftlearning.github.io/mslearn-ai-fundamentals/Instructions/Labs/02-content-safety.html

**Importante!** É necessário que você tenha uma conta da Microsoft criada para realizar este experimento! 

1. Acesse o portal do Azure em [https://portal.azure.com](https://portal.azure.com) usando suas credenciais de acesso da sua conta Microsoft.
2. Selecione **+ Criar um recurso**, pesquise por **Machine Learning** e crie um novo recurso do Azure Machine Learning da seguinte forma:
   - **Assinatura:** Sua assinatura do Azure.
   - **Grupo de recursos:** Crie ou selecione um grupo de recursos já existente.
   - **Nome:** Insira o nome do seu workspace.
   - **Região:** East US.
   - **Conta de armazenamento:** Não é necessária nenhuma alteração.
   - **Cofre de chaves:** Não é necessária nenhuma alteração.
   - **Application Insights:** Não é necessária nenhuma alteração.
   - **Registro de contêiner:** Não é necessária nenhuma alteração.
3. Selecione **Revisar + Criar** e, em seguida, **Criar**. Aguarde a criação e acesse assim que ela estiver concluída.

---

## Iniciando o Azure Machine Learning Studio

1. Dentro do workspace criado, selecione **Launch Studio**.
2. Ignore e feche qualquer mensagem que surgir.
3. No Studio, procure pelo seu workspace recém criado.

---
### Treinando um modelo usando aprendizado de máquina (Machine Learning)

1. Acesse a opção ML Automatizado.
2. Crie um novo Trabalho ML automatizado da seguinte forma: 

#### **Configurações básicas**
- **Nome do trabalho:** Não é necessário realizar alteração.
- **Nome do experimento:** `mslearn-bike-rental`
- **Descrição:** Descreva da maneira que acreditar mais adequada

#### **Tarefa e dados**
- **Tipo de tarefa:** Regressão.
- **Conjunto de dados:** Criar um novo conjunto de dados com as configurações:
  - **Nome:** `bike-rentals`
  - **Descrição:** Dados históricos de aluguel de bicicletas.
  - **Fonte de dados:** Arquivo local.
  - **Armazenamento:** Azure Blob Storage (`workspaceblobstore`).
  - **Upload:** Faça o download do arquivo zip no destino: [https://aka.ms/bike-rentals](https://aka.ms/bike-rentals). Após isso, extraia os dados de dentro do arquivo e realize o upload deles.

#### **Configurações da tarefa**
- **Coluna alvo:** `rentals`
- **Métrica principal:** `NormalizedRootMeanSquaredError`
- **Modelos permitidos:** `RandomForest`, `LightGBM`
- **Limites:**
  - **Máximo de testes:** 3
  - **Testes concorrentes:** 3
  - **Tempo limite do experimento:** 15 minutos
  - **Encerramento antecipado:** Ativado
 
- **Validação e teste**
  - **Tipo de validação:** Divisão treino-validação
  - **Percentual de dados de validação:** 10%
  - **Conjunto de testes:** Nenhum

#### **Computação**
- **Tipo de computação:** Serverless
- **VM:** `Standard_DS3_V2` ( Caso não esteja disponível, selecione qualquer uma das outras opções)
- **Número de instâncias:** 1

3. Envie o trabalho de treinamento.

Esta etapa provavelmente vai demorar alguns minutos para finalizar. Assim que concluída, siga para a próxima instrução.

## Revisar o melhor modelo

1. Na guia **Visão geral** dentro do ML Automatizado, veja o resumo do melhor modelo e clique em seu nome.
2. Na guia **Métricas**, selecione os gráficos **residuals** e **predicted_true**, removendo todos os outros.
3. Tudo pronto para analisar os gráficos a fim de avaliar o desempenho do modelo.

## Implantar e testar o modelo

1. Na guia **Modelo**, selecione **Implantar** > **Ponto de extremidade em tempo real**.
2. Configure:
   - **VM:** `Standard_DS3_v2`
   - **Contagem de instâncias:** 3
   - **Nome do ponto de extremidade:** Não é necessário alterar.
   -**Ponto de extremidade: ** Novo.
   -** Nome da implantação:** Deixe o padrão.
   -**Coleta de dados de inferência: ** Desativada.
   -**Pacote de modelo:** Desativado.
3. Aguarde a implantação ser concluída.

# Testar o serviço implantado

Agora você pode testar seu serviço implantado.

No Azure Machine Learning Studio, no menu à esquerda, selecione **Endpoints** e abra o ponto de extremidade em tempo real **predict-rentals**.

Na página do ponto de extremidade em tempo real **predict-rentals**, visualize a guia **Test**.

No painel **Input data to test endpoint**, substitua o JSON de modelo pelo código a seguir:

```json
{
  "input_data": {
    "columns": [
      "day",
      "mnth",
      "year",
      "season",
      "holiday",
      "weekday",
      "workingday",
      "weathersit",
      "temp",
      "atemp",
      "hum",
      "windspeed"
    ],
    "index": [0],
    "data": [[1,1,2022,2,0,1,1,2,0.3,0.3,0.3,0.3]]
  }
}
```
Agora, clique no botão Test.
Observe os resultados do teste realizado, que incluem o número previsto de aluguéis com base nas características de entrada. Ele deverá estar próximo de:
```json

[
   352.3564674945718
]
```
Em resumo, você usou um conjunto de dados históricos de aluguel de bicicletas para treinar um modelo. Ele prevê o número de aluguéis de bicicletas esperados em um determinado dia, com base em características sazonais e meteorológicas fornecidas a ele por você.

_________________________
# Limpeza
Se não for mais necessário, exclua os recursos gerados, para evitar cobranças desnecessárias, faça isso da seguinte forma:
1.	No Azure Machine Learning Studio, exclua o workspace criado.
2.	No portal do Azure, exclua o grupo de recursos associado ao workspace.

