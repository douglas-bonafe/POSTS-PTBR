#Jenkins e “deamons”: por que vocês me quebram? E como resolver em UMA linha.

Já se falou bastante sobre integração contínua no [blog da CS](http://blog.concretesolutions.com.br/?s=integra%C3%A7%C3%A3o+cont%C3%ADnua). Mas nunca é demais falar de CI (Continuous Integration), não é? Afinal, se você escreveu um código com testes e não tem a prática de CI e CD (Continuous Delivery), então você não está fazendo direito! E se você não está fazendo testes, aí é que eu tenho mesmo pena de você!
O Jenkins, como bem sabemos, garante o CI! Com ele e alguns plugins podemos fazer coisas como “buildar” os projetos todos em ordem, encadeando-os, disparar os testes automaticamente, gerar relatórios, etc. e tudo isso, por exemplo, sempre que o dev Joãozinho de Paula fizer um push na branch wharever de um dado repositório, ou a cada n minutos ou ainda numa hora específica.
O Jenkins é altamente personalizável e você pode, por exemplo, inserir comandos shell. O Jenkins executa esses comandos com os privilégios do usuário no escopo do grupo no qual ele executa no sistema operacional. Ao iniciar um job, ele instancia um bash onde executa os processos e, ao sair, destrói esse bash e tudo que ele criou, persistindo apenas o que foi explicitamente especificado para ele persistir na variável WORKBANCH. Agora, suponha que nosso build atualiza um servidor com informações do projeto, por exemplo, ou um emulador do android, ou qualquer aplicação que não queiramos que caia após o Job terminar…
Isso não é possível fazer por padrão no Jenkins, já que, por padrão, ele sempre limpa a bagunça que faz. Graças ao ProcessTreeKiller!!!
Como, então, resolvemos esse problema?
É assustadoramente simples! Mas até achar na documentação pode ir um tempinho e até lembrar como foi feito, mais um tempinho...
Basta alterar a variável de ambiente BUILD_ID.
Ou seja, o Jenkins não permite Deamons por padrão! Mas nós descobrimos um workaround! Parece besta, mas é uma mão na roda!

```
BUILD_ID=dontKillMe python wsgi.py
```

Para exemplificar, abaixo, coloco um cenário fictício. O script abaixo sobe um docker com jenkins e outro com uma aplicação Python que roda num servidor. Ao se fazer o push, o Jenkins constrói a aplicação Python e a dispara. Após a execução do job, vemos que o servidor permanece no ar. Depois disso, basta derrubar o script.


Nosso primeiro passo será criar uma pequena aplicação com Flask. Para economizar tempo criarei um HelloWorld. Num outro post melhoramos isso!
Mas para não ficar tão cru, vamos criar essa nossa aplicação no OpenShift. Para tanto, teremos a ardua tarefa de inserir UM COMANDO.


Criando uma aplicação Python Flask no Openshift
Considerando que você já possui uma conta no OpenShift, primeiramente instale o ruby e o rhc em sua máquina local.
Tendo instalado, execute:

```
$ rhc app create exampleflask python-2.7
```

Coloque seu usuário e senha no OpenShift, gere sua chave ssh e o rhc irá criar sua aplicação e te retornar algo como a mensagem abaixo no console.

```
  URL:        http://exampleflask-dsbonafe.rhcloud.com/
  SSH to:     ...@exampleflask-dsbonafe.rhcloud.com
  Git remote: ssh://...@exampleflask-dsbonafe.rhcloud.com/~/git/exampleflask.git/
  Cloned to:  .../exampleflask
```

Minha aplicação está a mais basica possivel. É apenas um HelloWorld para exemplificar como criar uma aplicação python no Openshift. O primeiro passo de uma aplicação flask é criar a estrutura padrão de diretórios (Ela vai subir se você não criar, todavia para fazer qualquer coisa além de um HelloWorld vai precisar dela). A estrutura que deve ser criada encontra-se abaixo.

```
exampleflask/
├── flaskapp.py
├── flaskr
│   ├── flaskr.py
│   ├── static
│   └── templates
├── requirements.txt
├── setup.py
└── wsgi.py
```

No nosso arquivo de dependências (requirements.txt) iremos adicionar a dependência do Flask.

```
Flask==0.10.1
```

No nosso arquivo do flaskapp.py vamos colocar nossa aplicação flask HelloWorld:

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```

No arquivo wsgi.py, adicionamos o import:

from flaskapp import app as application

Setamos o virtualenv para o openshift:

```
virtenv = os.path.join(os.environ.get('OPENSHIFT_PYTHON_DIR','.'), 'virtenv')
```

E dizemos que ele irá executar a partir de um servidor, trocando o método que chamamos do httpd (httpd.handle_request()):

```
httpd.serve_forever()
```

Sei que é ridícula esta aplicação, mas a intenção deste post não é o Flask nem o Python!!!

Depois disso, faremos um push na pasta do projeto:

```
$ git add --all
$ git commit -m "Adding Flask application"
$ git push
```

Agora nosso próximo passo será subir um container docker com Jenkins!!!
Mas para brincarmos um pouco, utilizarei o docker-compose.

Subir Jenkins com docker-compose

Primeiro crie um diretorio docker-jenkins e, dentro dele, escreva um Dockerfile:

```
FROM jenkins

COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN /usr/local/bin/plugins.sh /usr/share/jenkins/plugins.txt
ENV HOME $JENKINS_HOME

USER root
RUN \
  apt-get update && \
  apt-get install -y \
    graphviz \
    npm

RUN rm -rf /var/lib/apt/lists/*
USER jenkins
```

Depois, crie o docker-compose.yml:

```
master:
  image: jenkins
```

Daí volte para a linha de comando e digite:

```
$ sudo docker-compose build master
```

Tão simples quanto isso!

Garantindo que o deamon não vai cair

Dentro do seu job, num shell, coloque nossa linha de comando mágica!!!

```
BUILD_ID=dontKillMe python wsgi.py
```

Problema resolvido!!!

Então pessoal... é isso! Espero que tenha ajudado!
