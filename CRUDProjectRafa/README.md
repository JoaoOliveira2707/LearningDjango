# LearningDjango

NOTA: para instalar dependencias, ir para o diretorio `..\data\interim` e correr `pip install -r requirements.txt`.

Este projeto tem como objetivo a criação de tabelas pre-estabelecidas com comandos SQL recorrendo a Psycopg2 usando os scripts `create_tables.py` e `create_tables_no_user_input.py`.
A base de dados terá uma estrutura STAR onde a tabela crudapp_a_student será a tabela principal, e as tabelas crudapp_b_grade, crudapp_c_lonely, crudapp_d_stress e crudapp_e_dinning serão as tabelas externas, tendo como PK, FK o atributo student_id da tabela crudapp_a_student. É recomendado que os ficheiros a serem inseridos estejam todos numa unica pasta principal como a pasta `processed`.

A principal diferença entre o script `create_tables.py` e `create_tables_no_user_input.py` passa pelo simples facto do primeiro pedir inputs ao usuario como por exemplo, a pasta onde se encontram os ficheiros csv para adicionar as tabelas na base de dados (i.e. pasta com o nome `processed`) ou uma opção que permita alterar o localhost, password, username, etc, diretamente na linha de comando, sem ter que ir ao ficheiro de configuração (`config`) como é o exemplo da opção 3 no menu. Já no script `create_tables_no_user_input.py`, tem opções mais reduzidas e usa o ficheiro de configuração "`config`" para ir buscar os diretórios. Em principio, não será necessário alterar o ficheiro config com os diretórios absolutos, isto porque o ficheiro config faz uso de notação como dir = "`..\processed`" e, portanto, basta que o usuário coloque o diretório de trabalho na pasta "`interim`" (que tem o ficheiro config e os scripts), correr os scripts, e não será necessário alterar as configurações com os diretórios absolutos (como a pasta processed ou raw). Por exemplo, é possivel fazer `cd C:\Users\Nome do utilzador\nome da pasta\data\interim` e fazer `python create_tables.py` ou `python create_tables_no_user_input.py` que será suficiente para correr os scripts. Antes desta etapa é possível que seja necessário instalar dependências, portanto, estando no diretório `..\data\interim`, pode fazer `pip install -r requirements.txt` para instalar as dependências antes de correr os scripts.

Ambos os scripts têm duas funções em comum: A primeira opção que pretende criar um ficheiro csv chamado crudapp_e_dinning.csv (guardado em `.\processed`) a partir dos vários ficheiros .txt com informação sobre dinning (`.\raw\dinning`) e a segunda opção que vai gerar dados aleatórios que serão usados para criar um ficheiro csv chamado crudapp_a_student.csv e guardado na pasta `.\processed`. Este ficheiro será o ficheiro principal usado para criar primary keys (student_id), por exemplo, permitindo criar foreign keys com as restantes tabelas.

É importante referir que o projeto deverá ter uma estrutura base como a seguinte:

```
Pasta Exemplo (pode ter um nome qualquer)
\_|---- interim
\_\_|---- ficheiro de configuração (config)
\_\_|---- script que permite inputs do usuario (create tables.py)
\_\_|---- script com minimo input do usuario (create tables no user input.py)
\_|---- processed
\_\_|---- ficeiros que vão ser inseridos na tabela. NOTA: o nome dos ficheiros nao deve ser alterado e devem ter a mesma estrutura base (`crudapp_a_...`, `crudapp_b_...`, etc) pois os nomes destes ficheiros serão usados para adicionar os dados de forma dinâmica nas tabelas previamente criadas pelo script. No fundo, o nome das tabelas deve ser o mesmo que o nome dos ficheiros csv.
\_|---- raw
\_\_\_|--- dinning
\_\_\_\_| ---- ficheiros dinning em formato .txt. Estes dados serão usados pela primeira opção dos 2 scripts para concatenar todos os ficheiros .txt num unico ficheiro .csv (crudapp_e_dinning.csv) que será guardado na pasta processed
```

De modo a gerar os dados para a tabela central crudapp_a_student, foi feita uma função `random_student_data()` que vai escolher valores aleatorios provenientes de listas de valores pre-estabelecidos como indicado no project proposal. Estes dados sao depois guardados num ficheiro csv com o nome crudapp_a_student.csv na pasta processed.

Tendo os dados dos estudantes e as tabelas pre-criadas, passamos agora a introduzir os dados de forma dinâmica. Deste modo foi desenvolvida uma função chamada `insert_csv()` para inserir os dados. Dependendo do script usado, o usuario poderá ter que adicionar ou não a pasta onde os ficheiros .csv se encontram.

    NOTA: É importante referir novamente que o prefixo do nome dos ficheiros deve ser o mesmo que o nome das tabelas. Isto porque o nome dos ficheiros será usado para introduzir os dados nas tabelas de forma dinâmica. O nome dos ficheiros é extraido usando o metodo .split(".").

    Ainda de referir que a tabela central do modelo de dados usado deve ser ser a "crudapp_a_student". Os prefixos "crudapp_a_","crudapp_b_","crudapp_c_", "crudapp_d_" e "crudapp_e_" vao fazer com que os ficheiros apareçam no diretório de forma sequencial, permitindo a inserção das tabelas na ordem correta. A tabela mais importante de aparecer em primeiro lugar no diretório para a inserção dos dados é a "crudapp_a_student" pois as restantes ficam dependentes do atributo student_id de modo a criar referencias do tipo Foreign Key.

Nem todas as tabelas (ficheiros csv) têm exatamente os mesmos student_ids presentes na tabela central crudapp_a_student. Por exemplo, a tabela crudapp_b_grade nao tem os ids dos estudantes u20 e u21. Os scripts também tem em conta essta questão atraves da criação de um loop que guarda os student_ids disponiveis (provenientes da tabela crudapp_a_student) e faz a comparação entre as tabelas a serem inseridas. Se o uID existir na tabela central crudapp_a_student, entao os dados sao inseridos, caso contrario, o script dá "skip" a essa linha de informação do ficheiro csv. Isto tambem vai permitir que o script nao pare se existirem outros ficheiros com nomes diferentes das tabelas criadas (É assim possivel passar o diretório de uma pasta que tenha os 5 ficheiros com os mesmos nomes das tabelas pre-criadas assim como outro tipo de ficheiros que o script nao irá dar erro)

Por fim, ambos os scripts também geram um gráfico proveniente do package usando a package `seaborn`. Este grafico pretende ver uma simples relação entre o GPA dividido por genero dos alunos.

O menu disponivel no script create_tables.py é:
1 - Transform .txt files from dinning data into a single .csv file (not required if you already have the crudapp_e_dinning.csv file)
2 - Generate random data for the crudapp_a_student table. This will generate a csv file with the name `crudapp_a_student.csv`.
3 - Create all the tables (crudApp_a_student, crudApp_b_grade, crudApp_c_lonely,crudApp_d_stress,crudApp_e_dinning)
4 - Insert the data in the tables.
5 - Print the plot (distribution of overall GPA per subject/field of study)
6 - This option will 1) create tables, 2) ask for the directory with the csv files to add to the tables and 3) print the plot (essentially doing option 3,4,5 all together).
7 - Exit

O menu disponivel no script create_tables_no_user_input.py é:
1 - Transform .txt files from dinning data into a single .csv file (not required if you already have the crudapp_e_dinning.csv file)
2 - Generate random data for the crudapp_a_student table. This will generate a csv file with the name `crudapp_a_student.csv`.
3 - I just want the minimum user input possible! This option will 1) create tables, 2) add the csv files to the tables accordingly with the config path and 3) print the plot.
4 - Exit

APLICAÇÃO CRUD

- Referir que usuário deve ir para a sub-pasta "CRUDProject", etc
