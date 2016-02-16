# Modelagem de Banco de Dados Relacionais em projetos Reais

## Cenário atual

Muitas vezes no ambiente empresarial, especialmente no mundo do SCRUM, tem-se a idéia de que Modelagem de Dados implica em perda de tempo.
O SCRUM segue um modelo interativo e incremental que provoca mudanças muito rápidas no sistema.
No modelo *Waterfall* busca-se ter uma idéia clara desde o início do projeto e mapear, da forma mais completa possível todas as *features* do produto.
Cada vez mais empresas vem se dando conta de que esse sonho do modelo cascata é impossível ser alcançado. Especialmente em projetos de grande porte. E o que acontece é que, geralmente o que se foi especificado não consegue ser cumprido. Gera-se uma infinidade de relatórios inúteis que ninguém lê, calhamaços e mais calhamaços de documentação que, de tão distantes da implementação, nada significam e isso implica em tempo perdido e recursos desperdiçados.
Para resolver esses problemas do modelo cascata é que surge o SCRUM. O SCRUM não se arroga ser capaz de mapear nada de forma completa. Nele, nada é escrito em pedra. As centenas de páginas de relatório e SPECs não são sequer escritas no SCRUM e o foco é em fazer "a mágica acontecer".
Bem... mas com isso surgem diversas dúvidas por parte principalmente dos genrentes e CEOs. E sobre isso já existe bastante conteúdo bom publicado e eu recomendo a leitura do [livro do Jeff Sutherland](http://www.livrariacultura.com.br/p/scrum-a-arte-de-fazer-o-dobro-de-trabalho-na-metade-do-tempo-15065068).
Hoje nós vamos falar mais da visão dos DBAs. Mas aos interessados mais nas questões de negócio posso informar que há sim documentações. Todavia, isso é feito de forma muito mais otimizada!!!
Se a cada SPRINT tudo pode mudar no SCRUM, como fica a questão da modelagem de dados?

## A modelagem é descartável no SCRUM?

Já trabalhei em uma empresa que não fazia modelos dos seus negócios por julgar que no SCRUM os modelos são dispensáveis e que atrasam o desenvolvimento. Não era à toa que a o principal problema levantado em toda Retrospectiva era a comunicação e não poucas vezes, principalmente quando da entrada de novos membros no time, se verificava uma falta de compreensão do produto ou retrabalho.
Retrabalho em desenvolvimento ágil é uma heresia!!!
Mas a cultura interna pregava: modelagem é perda de tempo! Isso não dá certo para nós.
Mas será?
Vejamos, antes de responder, algumas vantagens da modelagem de dados:

* Modelar os dados estabelece um senso comum a respeito do negócio e dos objetos.
* Modelar os dados facilita a criação de uma terminologia que possa ser usada para facilitar a comunicação e o *onboarding* de novos membros no time.
* Modelar os dados facilita detectar buracos e inconsistências.
* Modelar os dados auxilia na determinação do escopo do sistema e na sua interface com o universo que lhe é exterior.
* Modelar os dados auxilia pessoas técnicas e não-técnicas a compreenderem melhor todo o sistema e comunicarem entre si com termos comuns.
* Modelar auxilia a dar manutenção.
* Modelar estimula a criatividade dos membros do time.

De fato, a modelagem consome um tempo importante do lifecycle do projeto. Todavia, apenas quando ela é feita nos moldes e com a pretensão da modelagem no Waterfall.
Porém, seria possível modelar de forma ágil? Ou seja... há sentido em se fazer um modelo de sistema que seja, junto com o código, interativo e incremental?
Seria possível aplicar o método Sashimi não apenas no desenvolvimento em si, quanto também em outros elementos do ciclo do projeto?
É bem sabido que o modelo Waterfall oferece diversas vantagens e foi um grande avanço em relação ao que se tinha no passado.
O SCRUM surge no cenário como uma revolução! E, como toda revolução, destrói coisas boas que existiam antes dela em nome de "bens maiores". Como toda revolução parte do erro de acreditar que "os fins justificam os meios".
Tanto é que a maioria das empresas que aplicam o SCRUM não o aplicam tal qual ele é. Elas criam suas adaptações de SCRUM, o SCRUM à sua maneira. Inclusive aquelas empresas que fazem propaganda de serem 100% SCRUM.

## A modelagem no lifecycle do SCRUM

A modelagem entra dentro do SPRINT, como sua primeira tarefa: avaliar e refatorar os modelos. Isso pode aparentar apenas confirmar aquela velha impressão de que vai gastar mais tempo. Além disso temos agora o problema de não ter todo o sistema mapeado minunciosamene como no Waterfall.
Entretanto são apenas impressões.
Se o time trabalha incrementalmente, suas alterações e migrações no banco de dados nunca serão suficientemente grandes ou complexas para causar problemas. Em outras palavras, a evolução da estrutura do banco de dados apenas acompanha a evolução do sistema como um todo.
A única exigência deste processo é a disciplina.
Alterações no modelo do banco devem ser comunicadas na daily e, se forem críticas, nada impede de, a qualquer momento, levantar-se uma bandeira para o restante do time.
Aplica-se aí também a idéia do sashimi. Deve-se buscar dividir às menores fatias funcionais toda e qualquer alteração nos modelos de forma a não impactar ninguém.
Um grande exemplo de como tais modelos funcionam na prática, pode ser visto no ciclo de desenvolvimento de um projeto no Django. Após qualquer alteração no banco de dados é obrigatória a execução do comando syncdb no Django.

## Modelando um pequeno sistema

Suponha que o cliente chegue com a seguinte descrição do projeto:

> "Eu tenho uma empresa de importação que efetua a suas compras através de contratos. Cada contrato (identificado por um número) firmado com um fornecedor, diz respeito a várias mercadorias (identificadas por um código e com um nome). Do contrato consta também a data da assinatura, o prazo de validade, a moeda e o valor. É fixado no contrato o preço unitário de compra de cada mercadoria, a quantidade especificada numa unidade de medida que é sempre a mesma para cada mercadoria independente do contrato. É necessário manter informação sobre os fornecedores (nome, endereço, telefone e fax) que são identificados por um código. As mercadorias envolvidas num contrato são todas enviadas num único transporte (identificado por um número). Para cada transporte é necessário conhecer o tipo de transporte, a data de partida e a data de chegada."

Usaremos aqui o Modelo Entidade-Relacionamento Estendido (MER-X). Assim, no nosso SPRINT Zero, nosso modelo seria como a seguir:

![alt text](https://github.com/douglas-bonafe/POSTS-PTBR/blob/master/figuras/modelagem%20em%20scrum/modelagem.png)

As alterações no modelo podem ser feitas no Dia e versionadas no GIT. Ou ainda podem ser exportadas no xml do Visio, pelo mesmo Dia ou pelo MS Visio.

Isso permite que os modelos sejam inclusive versionados e evoluam junto com o código.

Bem, pessoal, por hoje isso é tudo.
