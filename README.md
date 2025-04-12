# Sistema de inscrições em Eventos
Uma criação de um sistema para inscrever alunos em eventos. Também será possível a criação de eventos.

## Requisitos Funcionais
1. Inscrição
   - o usuário pode se inscrever no evento usando nome e e-mail.
2. Geração de Link de Indicação:
    - o usuário pode gerar um link de indicação (um por inscrito).
3. Ranking de Indicações:
    - o usuário pode ver o ranking de indicações.
4. Visualização de Indicações:
    - o usuário pode ver a quantidade de inscritos que ingressaram com seu link.

## User Stories
### US00 - CRUD de Evento
Este User Story é necessário para subsidiar os User Stories e Requisitos Funcionais existentes. Algumas funcionalidades para gerenciarmos eventos:

- Criação de um novo evento
- Listagem de todos os eventos disponíveis
- Recuperação dos detalhes de um determinado evento pelo ID
- Recuperação dos detalhes de um determinado evento pelo seu Pretty Name
~~~bash
Endpoint: POST /events
Descrição: Cria um novo evento
Requisição
{
	"name": "CodeCraft Summit 2025",
	"location": "Online",
	"price": 0.0,
	"startDate": "2025-03-16",
	"endDate": "2025-03-18",
	"startTime": "19:00:00",
	"endTime": "21:00:00"
}
~~~~

Resposta:
~~~bash
{
	"id": 1,
	"name": "CodeCraft Summit 2025",
	"prettyName": "codecraft-summit-2025",
	"location": "Online",
	"price": 0.0,
	"startDate": "2025-03-16",
	"endDate": "2025-03-18",
	"startTime": "19:00:00",
	"endTime": "21:00:00"
}
~~~~

### US01 - Realizar Inscrição
Este User Story atende aos requisitos funcionais RF01 e RF02
~~~bash
Endpoint: POST /subscription/PRETTY_NAME
~~~~

- O usuário poderá fazer inscrição em um evento previamente cadastrado na base de dados, informando seu nome e seu e-mail
- Como é um sistema onde podemos ter vários eventos, pode acontecer de um usuário já está em nossa base de dados por ter participado de eventos anteriores. Dessa forma, basta recuperar seus dados e realizar a inscrição
- O usuário não pode se inscrever duas vezes no mesmo evento. Se houver já uma inscrição no respectivo evento pelo usuário, uma mensagem de erro deverá ser enviada (conflito)
- Ao final da realização da inscrição, a resposta será um JSON com o número da inscrição no evento

Requisição Esperada:
~~~bash
{
	"userName": "John Doe",
	"email": "john@doe.com"
}
~~~~
Resposta Esperada:
~~~bash
{
	"subscriptionNumber": 1,
	"designation": "https://devstage.com/codecraft-summit-2025/123"
}
~~~~

Casos de usos

1. Caso base:
    - *Condições*: Evento previamente cadastrado, Usuário ainda inexistente (email não existe)
    - *Ações*:
        - Inserir usuário na base;
        - Adicionar nova inscrição para o usuário;
        - Retornar o resultado da inscrição contendo o ID e o link para indicação.

2. Caso Alternativo:
   - *Condições:* Usuário existe na base, porém não há inscrição dele
   - *Ações:*
       - Adiciona nova inscrição para o usuário
       - Retorna o resultado da inscrição contendo o ID e o link para indicação

3. Caso Alternativo:
   - *Condições*: Já existe inscrição do usuário no evento
   - *Ações*:
     	- Lançar uma exceção indicando conflito


### US02 - Gerar Ranking de Inscritos
Este User Story atende ao requisito funcional RF03.
~~~bash
Endpoint: GET /subscription/PRETTY_NAME/ranking?limit=X
~~~~
- Possibilidade de gerar um ranking de número de inscritos por indicação (ou seja, ordenado pela somatória de inscritos por indicação)
- Pode ser limitado aos X primeiros colocados no ranking

~~~bash
http://localhost:8080/subscription/codecraft-summit-2025/ranking?limit=3
[
	{
		"userName":"John Doe",
		"subscribers": 1000
	},
	{
		"userName":"Mary Page",
		"subscribers": 873
	},
	{
		"userName":"Frank",
		"subscribers": 690
	}
]
~~~~

### US03 - Gerar Estatísticas de número de inscritos por participante
Este User Story atende ao requisito funcional RF04.

~~~bash
Endpoint: GET /subscription/PRETTY_NAME/ranking/USERID
~~~~
Recuperar o número de inscritos de inscritos que efetivaram sua participação no evento indicados por um determinado usuário (USERID)

~~~bash
http://localhost:8080/subscription/codecraft-summit-2025/ranking/123
{
	"subscribers": 1267
}
~~~~
