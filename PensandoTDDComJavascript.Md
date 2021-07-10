# TDD com Javascript

## Pensando TDD com Javascript

TDD é uma técnica de desenvolvimento. Provavelmente você já leu muitos artigos falando sobre TDD. Talvez você até já experimentou alguma ferramenta de TDD ao desenvolver seus códigos. Mas você continua usando? Você sabe o valor de desenvolver um código testável?

Assisti muitas palestras sobre TDD, vi vários slides, li artigos e iniciei meus estudos, tentando colocar em prática tudo o que tinha visto.

“- Por que iniciar com baby steps?”
“- Por onde eu devo começar?”
“- Devo fazer testes nos meus arquivos já existentes?”,
“- O que eu devo escrever primeiro?”,
“- O que realmente eu preciso testar?”

Eram perguntas que sempre vinham a minha mente quando eu começava.

O nome do negócio é auto-explicativo: TDD - Test Drive Development, ou “Desenvolvimento Guiado por Testes“. Se o desenvolvimento deve ser guiado por testes, significa que eu preciso começar a escrever testes que irão guiar meu desenvolvimento, correto? Correto! Mas como fazer isso? Não é tão simples para começar.

Nas palestras que eu assistia, sempre mostravam as mesmas coisas:

- Escreva um teste
- Desenvolva em cima do teste algo com baby steps
- Faça o teste passar
- Refatore o código
- Escreva outro teste
- Continue nessa sequência

Eu começava a escrever meus testes e, chegava uma hora que eu já estava todo perdido, pois tinha feito um teste onde eu precisaria escrever uma lógica muito grande de código, e acabava deixando tudo de lado novamente.

Os princípios que eu ouvia estavam corretos, mas eu não conseguia começar sem ver um exemplo real.

Foi então que em Janeiro do ano passado (2014), eu assisti uma palestra do Caio Andrade sobre Artesanato de Software, onde ele falava de TDD e Código Limpo( o cara manja muito, sigam ele ae xD).

Trocando algumas ideias com ele, percebi que, o que faltava pra eu entender de vez o porquê de escrever testes, é que eu precisava pensar TDD e aprender a escrever código limpo de verdade.

## Clean Code

Na palestra do Caio, ele recomenda o livro Clean Code, que apesar de ser baseado em Java, os conceitos são possíveis de se aplicar em qualquer linguagem, inclusive Javascript.

Recomendo fortemente a leitura do livro, que tem inclusive versão em pt-BR, se você preferir :D

Vou procurar falar bastante de Clean Code nos meus artigos, citando algumas dicas enquanto estiver mostrando códigos. Mas sério, leia o livro. Você não vai se arrepender, e vai te ajudar muito a pensar de forma diferente enquanto estiver programando :)

## Como começar

Se você está começando agora com TDD, você precisa experimentar bastante, mas fora do seu ambiente de trabalho! Pratique muito as dicas passadas aqui até que fique clara a sua implementação. Se tiver dúvidas, poste nos comentários e vamos crescendo juntos! Também estou a pouco tempo escrevendo testes, mas quanto mais praticamos, mais aprendemos!

Inicialmente, vou mostrar como fazer testes usando Javascript do lado do servidor, com NodeJS. Mas em posts futuros, iremos ver também como implementar no client side, quando criarmos nossos próprios componentes, colocando em prática o que aprendemos nos posts sobre módulos!

### Preparando o ambiente

Vamos começar pela instalação das ferramentas necessárias para iniciarmos nossos testes. Iremos precisar de:

- NodeJS (óbvio);
- Mocha - Para rodar os testes. Deve ser instalado globalmente;
- Istanbul - Para Code Coverage. Também iremos instalar globalmente;
- ShouldJS - Para fazer as asserções. Esse módulo será instalado localmente.

### Mocha

O Mocha é a ferramenta que vamos utilizar para executar nossos testes, e que vai disponibilizar a interface que necessitamos para padronizar a escrita dos testes. Por questões de legibilidade, iremos utilizar o formato BDD.

BDD significa Behavior Driven Development, ou Desenvolvimento Guiado por Comportamento. Ele nada mais é do que uma forma mais “legível” (digamos assim), de escrever nossos testes.

Só pra você entender melhor: em uma interface com TDD (usando assert, você escreveria seu código mais ou menos assim:

```
var assert = require( 'assert' );
assert.equal( ( 1 + 2 ), 3, '1 + 2 should be equal 3' );
```

O módulo assert faz parte do core do NodeJS, e ele usa TDD. Seu método equal() recebe 3 parâmetros: o valor atual (que você quer testar), o valor esperado e uma mensagem que será mostrada no seu terminal quando esse teste rodar.

Os parâmetros não ficam implícitos ao olhar esse código pela primeira vez, e sem conhecer o módulo assert. Você sabe que ali tem 3 parâmetros, mas não sabe o que exatamente eles são até que você olhe a documentação.

Agora, olhe como ficaria o mesmo código usando BDD, com o módulo should:

```
var should = require( 'should' );
( 1 + 2 ).should.be.equal( 3 );
```

Precisa dizer algo? O should extende os objetos nativos do JS, adicionando uma propriedade should.
Essa propriedade é um objeto com outras propriedades e métodos que dizem exatamente o que é esperado do teste. Traduzindo o código acima, você pode ler:

```
1 + 2 deveria ser igual a 3.
```

Você também pode usar a variável should, que definimos ao usar require( 'should' ), dessa forma:

```
var should = require( 'should' );
should( 1 + 2 ).be.equal( 3 );
```

Que você terá o mesmo resultado. E a leitura é bastante semãntica.

Esse é o motivo de escolhermos BDD :)

Se quiser, depois você pode visitar o site do Mocha e ver quais são as outras interfaces que ele disponibiliza, e exemplos de cada uma delas, ok? Vamos em frente!

Perae, perae.. e o que é esse tal Istanbul?

Não comentei sobre ele porque ainda não tinha chegado a hora. Mas já que você perguntou, vamos lá:

## Code Coverage

O Istanbul vai nos ajudar a fazer a cobertura do nosso código. Quando você começa a escrever testes, como você vai saber se esses testes estão cobrindo todos os if's, switch's, function's, etc, do seu código? O Istanbul nos ajuda com isso. Ele mostra a porcentagem de quanto o seu código está coberto por testes, e o que ainda não está testado. Vai ficar mais fácil entender quando começarmos a mexer com ele :)

### Continuando…

Após instalar o NodeJS (se já não o tiver instalado), instale o Mocha e o Istanbul globalmente:

```
npm i -g mocha istanbul
```

Agora precisamos de um projeto para ver na prática como fazer nossos testes.

## Iniciando nosso projeto

Vamos criar um módulo de soma. Uma função que recebe dois números e retorna a soma desses dois números. Exemplo bem besta, para você ver do início como deve testar um código. Crie a seguinte estrutura para nosso projeto:

```
.
├── app.js
├── sum.js
└── /tests
    └── sumTest.js
```

O arquivo app.js executará nossa aplicação. O sum.js será nosso módulo responsável por fazer a soma.

E no diretório tests, iremos centralizar nossos testes.

Ainda não temos nenhum código escrito. A ideia de usar TDD (ou BDD), é fazer com que todo o nosso desenvolvimento seja guiado por testes. Então, antes de escrever qualquer código, precisamos dizer o que esperamos que o nosso código faça.

Parece insano não? Mas pense como se você estivesse criando uma documentação. Você vai escrever a interface do seu projeto, aquilo que você espera que seja público, mostrando como usá-lo. A diferença é que, com TDD, nós vamos garantir que essa interface realmente funciona como deveria.

No final, você vai ver que os seus testes são exatamente isso: a documentação do seu projeto :)

Mas antes de tudo, vamos instalar o should, pois sabemos que vamos precisar dele, como vimos no exemplo lá no começo do artigo:

```
npm i should
```
Então vamos começar pelo nosso arquivo test/sumTest.js:

```
var should = require( 'should' );
var sum = require( '../sum' );

describe( 'sum.js', function() {
  it( 'sum of 2 + 3 should return 5', function() {
    sum( 2, 3 ).should.be.equal( 5 );
  });
});
```

As funções describe() e it() fazem parte da interface do Mocha. O describe() cria um escopo de testes. No nosso caso, vamos usá-lo para testar todo o nosso módulo sum.js. O primeiro parâmetro que ele recebe é uma descrição para esse escopo. Vou deixar somente o nome do módulo, para simplificar.

O segundo parâmetro é a função que será executada quando rodarmos esse escopo.

A função it() é o que vamos usar como wrapper para o nosso teste. No primeiro parâmetro, você coloca a descrição do que você vai testar. O segundo parâmetro é a função que executará quando esse teste rodar.

Devemos usar somente um it() por teste. E cada teste deve testar apenas uma coisa por vez. Da mesma forma que, no seu código, cada método ou função vai ter apenas uma responsabilidade, seguindo o SRP (Single Responsibility Principle), devemos seguir o mesmo princípio para os testes: um it() por teste.

E dentro do it(), fazemos a asserção. O que nós queremos é que, ao passar dois números como parâmetro para nosso módulo, ele retorne a soma desses dois números.

Agora vamos executar nosso teste e ver o que vai acontecer:

```
mocha tests/sumTest.js
```

O resultado que você vai obter deve ser parecido com isso:

```
$ mocha tests/sumTest.js 
  
  
  sum.js
    1) sum of 2 + 3 should return 5
    
    
  0 passing (11ms)
  1 failing
  
  1) sum.js sum of 2 + 3 should return 5:
     TypeError: object is not a function
      at Context.<anonymous> (/var/www/tdd/tests/sumTest.js:6:5)
      at callFn (/usr/lib/node_modules/mocha/lib/runnable.js:251:21)
      at Test.Runnable.run (/usr/lib/node_modules/mocha/lib/runnable.js:244:7)
      at Runner.runTest (/usr/lib/node_modules/mocha/lib/runner.js:374:10)
      at /usr/lib/node_modules/mocha/lib/runner.js:452:12
      at next (/usr/lib/node_modules/mocha/lib/runner.js:299:14)
      at /usr/lib/node_modules/mocha/lib/runner.js:309:7
      at next (/usr/lib/node_modules/mocha/lib/runner.js:248:23)
      at Object._onImmediate (/usr/lib/node_modules/mocha/lib/runner.js:276:5)
      at processImmediate [as _immediateCallback] (timers.js:345:15)
```

Na linha 1 mostra o comando de execução do teste.
Na linha 4, mostra o texto que passamos para o describe().
Na linha 5 mostra o texto que passamos para o it(), descrevendo o teste.
Na linha 8 e 9, mostra quantos dos nossos passaram, e quantos falharam, e o tempo que demoraram para executar.
Na linha 11 ele mostra a descrição do teste que falhou.
Na linha 12 vai mostrar o motivo da falha: TypeError: object is not a function.

Você sabe por que deu erro? Porque ainda não temos a função o nosso módulo sum. Mas no nosso teste, nós fizemos o require dele na variável sum. E dentro do nosso teste, esperávamos que ele fosse uma função, recebendo dois números e retornando a soma dos mesmos.

Acabamos de concluir com sucesso a primeira parte dos testes: o RED. Lembra dos passos completos de um teste, que você vê em todo lugar? Se não sabe, os passos são esses:

RED - Fazer um teste para quebrar;
GREEN - Escrever um código, usando baby steps, para que o teste passe;
REFACTOR - Com o teste passado, refatorar o código, se necessário, para que funcione de maneira aceitável.
Agora vamos para o segundo passo: fazer nosso código funcionar, com o mínimo de código possível.

No arquivo sum.js:

```
module.exports = function sum( number1, number2 ) {
  return 5;
}
```

Por que você retornou 5 direto?

Isso são baby steps. Você não vai fazer toda a implementação do seu código agora, mas somente o MÍNIMO NECESSÁRIO para que o teste passe. Qual o resultado que o nosso teste precisa para passar? Não é 5? Então iremos retornar 5. Logo você vai entender melhor.

Vamos rodar o teste novamente:

```
mocha tests/sumTest.js
```

E o resultado:

```
$ mocha tests/sumTest.js 
  
  sum.js
    ✓ sum of 2 + 3 should return 5 
    
  
  1 passing (10ms)
```

Nosso teste passou! Aeeee!! Já temos o GREEN.

O que precisamos fazer agora?

Passamos nesse primeiro teste pelo RED e pelo GREEN. Agora temos que verificar se o nosso código está sujo, se tem algo que poderia melhorar nele para que seja mais legível. Essa é a etapa do REFACTOR.

O que pode ser melhorado nesse código? A resposta é: NADA!

Mas como NADA? Você tá louco? Vai deixar um número fixo no código? Tá maluco? Não era pra somar? Vou sair daqui, não quero ler mais!

Que rebeldia é essa jovem? Senta aqui, vamos conversar.

Calma jovem. Você vai entender isso agora. Os testes servem para testar todas as possibilidades de entrada de dado que podem chegar ao nosso código. Então ainda não acabou. Vamos testar mais uma possibilidade.

Voltando ao tests/sumTest.js, adicione logo após o primeiro it():

```
it( 'sum of 10 + 10 should return 20', function() {
  sum( 10, 10 ).should.be.equal( 20 );
});
```

Vamos rodar nosso teste novamente:

```
mocha tests/sumTest.js
```

E temos como resultado:

```
$ mocha tests/sumTest.js 
  
  
  sum.js
    ✓ sum of 2 + 3 should return 5 
    1) sum of 10 + 10 should return 20
    
    
  1 passing (14ms)
  1 failing
  
  1) sum.js sum of 10 + 10 should return 20:
      
      AssertionError: expected 5 to be 20
      + expected - actual
      
      +20
      -5
      
      at Assertion.fail (/var/www/tdd/node_modules/should/lib/assertion.js:113:17)
      at Assertion.prop.(anonymous function) (/var/www/tdd/node_modules/should/lib/assertion.js:39:14)
      at Context.<anonymous> (/var/www/tdd/tests/sumTest.js:10:29)
      at callFn (/usr/lib/node_modules/mocha/lib/runnable.js:251:21)
      at Test.Runnable.run (/usr/lib/node_modules/mocha/lib/runnable.js:244:7)
      at Runner.runTest (/usr/lib/node_modules/mocha/lib/runner.js:374:10)
      at /usr/lib/node_modules/mocha/lib/runner.js:452:12
      at next (/usr/lib/node_modules/mocha/lib/runner.js:299:14)
      at /usr/lib/node_modules/mocha/lib/runner.js:309:7
      at next (/usr/lib/node_modules/mocha/lib/runner.js:248:23)
      at Object._onImmediate (/usr/lib/node_modules/mocha/lib/runner.js:276:5)
      at processImmediate [as _immediateCallback] (timers.js:345:15)

```

Na linha 5, mostra que o nosso primeiro teste continua passando.
Na linha 6, mostra que o nosso segundo teste quebrou.
A partir da linha 12, mostra a descrição do teste que quebrou: AssertionError: expected 5 to be 20. Ou seja: houve um erro na asserção: era esperado que, o 5 que a função está retornando fosse igual a 20.

Na linha 15 mostra que, nas linhas abaixo, o que está com o sinal de + é o esperado (expected), e o que está com o sinal de - é o valor atual (actual), que foi retornado.

Voltamos ao ciclo, passando com sucesso pelo RED, criando uma nova possibilidade que quebra nosso código.

Tá conseguindo compreender agora o que estamos fazendo? Imagine os testes como aquele usuário que acessa seu site, fazendo aquelas ações que você sempre diz: - Não, o usuário nunca vai fazer isso - e é exatamente o que ele faz :P

Os testes vão garantir que, qualquer bizarrice que o seu código receber como entrada, vai ser tratado corretamente para devolver sempre o valor correto no final. Agora tá ficando legal né, fala a verdade :D

Então, vamos refatorar nosso código, para fazer com que o segundo teste passe. Lembrando que o primeiro teste não deve quebrar, pois já passamos por ele. Agora os dois testes precisam passar. Como vamos fazer isso?

Voltamos ao sum.js e mudamos o nosso return:

```
module.exports = function sum( number1, number2 ) {
  return number1 + number2;
}
```

Então, executamos novamente nosso teste:

```
mocha tests/sumTest.js
```

E temos o resultado:

```
$ mocha tests/sumTest.js 
    
  sum.js
    ✓ sum of 2 + 3 should return 5 
    ✓ sum of 10 + 10 should return 20 
    
    
  2 passing (14ms)
```

Uhuull!! Passamos mais um teste! o/

Agora, verificamos se podemos refatorar alguma coisa. Nos dois casos, não houve necessidade de refactor. O código está bem simples e legível. Fizemos somente uma função simples, que retorna dois números passados como parâmetro. Mas aí eu te pergunto: dá pra testar mais alguma coisa ainda nesse código?

Se você começar a pensar nas possibilidades, dá sim. Mas esse post já está grande demais. Como lição de casa, vou deixar para VOCÊ JOVEM, complementar isso e fazer mais alguns testes. Por exemplo: você pode testar se o usuário vai entrar algum valor diferente de número. Se entrar, você precisa ver o que é o mais correto retornar: dispara um erro? Retorna undefined? Você decide. O módulo é seu. Você só precisa testar as possibilidades para ver se ele retorna o que realmente você espera.

Quando você terminar de testar o seu módulo, e executar o mocha com todos os testes passados, vai perceber que tem a documentação completa do seu módulo, dizendo exatamente o que ele faz, o que ele espera e o que ele retorna :D

https://mochajs.org/
https://github.com/gotwarlost/istanbul
https://github.com/shouldjs/should.js

[Artigo original](https://blog.da2k.com.br/2015/01/06/pensando-tdd-com-javascript/)
