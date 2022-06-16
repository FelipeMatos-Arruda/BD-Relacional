# BD Relacional

Usando a linguagem SQL com Python, criando assim um ambiente de análise poderoso com Banco de Dados Relacional.

O arquivo usado nesse projeto foi: https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database

### Instalando e Carregando os Pacotes

Usamos o comando `pip install` para instalar pacotes na sua máquina.

```
pip install pandas 
pip install sqlite3
pip install ipython-sql
```
Depois disso usaremos o comando `import` para carregar os pacotes e usá-los

```
import pandas as pd
import sqlite3
```
### Banco Relacional, Python e SQL Para Análise de Dados

Temos em mãos um arquivo com dados de pacientes que desenvolveram ou não diabetes. Precisamos gerar uma amostra de dados com os pacientes com mais de 50 anos e para cada um deles indicar em uma nova coluna se o paciente está normal (BMI menor que 30) ou obeso (BMI maior ou igual a 30). Então devemos gerar um novo arquivo CSV e encaminhar para o Cientista de Dados.

```
#Vamos criar um DataFrame usando a biblioteca pandas, com base no nosso arquivo diabetes.
df = pd.read_csv('dataset/diabetes.csv')

# Usando o type para retornar o tipo de classe do argumento (objeto)
type(df)
```

```
#shape retorna o número de linhas e o número de colunas do nosso dataframe.
df.shape
```

```
Retorna as 5 primeiras linhas do nosso Dataframe, importante para ver como o pandas trouxe o nosso arquivo.
df.head()
```
Queremos usar comandos SQL para resolver o problema, então precisamos criar uma conexão de banco de dados. Ele vai gerar um banco vazio onde nós colocaremos as informações do dataset dentro. 

```
# Criamos a conexão a um banco de dados SQLite
cnn = sqlite3.connect('database/dbprojeto1.db')
```
Agora preenchendo o database.

```
# Copia o dataframe para dentro do banco de dados como uma tabela
df.to_sql('diabetes', cnn)
```
Apartir de agora podemos usar SQL para fazer consultas.

```
%%sql

SELECT count(*) FROM diabetes

#Retornará 768 registros.
```

Vamos ver quantos pacientes tem a glicose maior que 190
```
%%sql

SELECT Age, Glucose, Outcome FROM diabetes WHERE Glucose > 190
```
![image](https://user-images.githubusercontent.com/84130785/174133543-79099c8d-9557-4e9d-a36b-bf7a298a6bb5.png)

















