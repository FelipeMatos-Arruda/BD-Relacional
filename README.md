# BD Relacional

Usando a linguagem SQL com Python, criando assim um ambiente de análise poderoso com Banco de Dados Relacional.

O dataset usado nesse projeto foi: https://www.kaggle.com/datasets/uciml/pima-indians-diabetes-database

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
![image](https://user-images.githubusercontent.com/84130785/174140677-e90fdeeb-d6e0-4258-a096-58791639e898.png)

```
#shape retorna o número de linhas e o número de colunas do nosso dataframe.
df.shape
```
![image](https://user-images.githubusercontent.com/84130785/174133927-2d16e696-e4fa-4ed8-85ce-9433682bdda7.png)

A função `head()` Retorna as 5 primeiras linhas do nosso Dataframe, importante para ver como o pandas trouxe o nosso arquivo.

```
df.head()
```
![image](https://user-images.githubusercontent.com/84130785/174133853-39f70f48-770b-43c6-b55d-db7b8af43f03.png)


Queremos usar comandos SQL para resolver o problema, então precisamos criar uma conexão de banco de dados. 
Ele vai gerar um banco vazio onde nós colocaremos as informações do dataset original dentro. 

```
# Criamos a conexão a um banco de dados SQLite
cnn = sqlite3.connect('database/dbprojeto1.db')
```
Agora preenchendo o database...

```
# Copia o dataframe para dentro do banco de dados como uma tabela
df.to_sql('diabetes', cnn)
```
Apartir de agora podemos usar SQL para fazer consultas.

```
%%sql
SELECT count(*) FROM diabetes
```
Retornará 768 registros
![image](https://user-images.githubusercontent.com/84130785/174140948-ad874faf-a118-42cb-ae98-4d1f44c1a09e.png)

Vamos ver quantos pacientes tem a glicose maior que 190 ?
```
%%sql
SELECT Age, Glucose, Outcome FROM diabetes WHERE Glucose > 190
```
Outcome é se o paciente desenvolveu diabetes ou não.

![image](https://user-images.githubusercontent.com/84130785/174133543-79099c8d-9557-4e9d-a36b-bf7a298a6bb5.png)

Quais colunas nos temos no nosso Dataframe ?

```
#Retorna as colunas de um DataFrame
df.columns
```
Para entregar os dados ao Cientista de Dados, vamos criar uma Tabela usando o SQL com as colunas importantes para a análise.
Usamos o método `CREATE TABLE` e especificamos nome e o tipo da tabela.

```
%%sql

CREATE TABLE pacientes (Pregnancies INT,
                        Glucose INT,
                        BloodPressure INT,
                        SkinThickness INT,
                        Insulin INT,
                        BMI DECIMAL(8, 2),
                        DiabetesPedigreeFunction DECIMAL(8, 2),
                        Age INT,
                        Outcome INT)
```

Vamos preencher alguns dados do dataset original na nossa nova tabela, traremos dados de pacientes com mais de 50 anos.

```
%%sql

INSERT INTO pacientes(Pregnancies, 
                      Glucose, 
                      BloodPressure, 
                      SkinThickness, 
                      Insulin, 
                      BMI, 
                      DiabetesPedigreeFunction, 
                      Age, 
                      Outcome) 
SELECT Pregnancies, 
       Glucose, 
       BloodPressure, 
       SkinThickness, 
       Insulin, 
       BMI, 
       DiabetesPedigreeFunction, 
       Age, 
       Outcome 
FROM diabetes WHERE Age > 50;

```
Para saber se deu tudo certo é só chamar a tabela SQL.

```
%%sql
SELECT * FROM pacientes
```
![image](https://user-images.githubusercontent.com/84130785/174135150-626d9b95-8648-4620-ac1c-1cbede7f0594.png)

Agora vamos criar uma nova coluna na tabela de pacientes, para respondermos a próxima pergunta do problema. 
Alteramos a tabela de paciente, adicionando a coluna perfil.

```
%%sql 
ALTER TABLE pacientes
ADD Perfil VARCHAR(10);
```

Vamos atribuir normal na nova coluna onde BMI for menor do que 30.

```
%%sql
UPDATE pacientes
SET Perfil = 'Normal'
WHERE BMI < 30;
```

Vamos atribuir obeso onde BMI foi maior do que 30.

```
%%sql
UPDATE pacientes
SET Perfil = 'Obeso'
WHERE BMI >= 30;
```

Vamos ver o resultado.

```
%%sql
SELECT * FROM pacientes
```

![image](https://user-images.githubusercontent.com/84130785/174139848-966a2e47-40f4-4ff5-8323-1a47006392cd.png)

### Carregando os Dados no Pandas e Salvando o CSV

Vamos fazer uma série de atributos para atualizar o nosso Database e também exportar a tabela com as transformações.

```
# Query
query = cnn.execute("SELECT * FROM pacientes")
query
```

Para cada coluna no resultado da minha Query, busque cada uma das colunas. Basicamente é um loop.

```
# List Comprehension
cols = [coluna[0] for coluna in query.description]
cols
```
Gerando um novo DataFrame com o cols.

```
resultado = pd.DataFrame.from_records(data = query.fetchall(), columns = cols)
```

Por último salvar o arquivo no formato csv

```
resultado.to_csv('dataset/pacientes.csv', index = False)
```
