# TDD em Javascript

## TDD Contínuo em JavaScript com Node.js e Gulp

Quem pratica TDD em seu dia a dia conhece os benefícios de um código mais limpo, testado e fácil de modificar. Esses benefícios, no entanto, não vêm automaticamente, pois é preciso muito estudo e prática para compreender bem a técnica e saber aplicá-la.

Considere a recomendação de dar passos curtos, ou seja, testar o código aos poucos. A grande vantagem é que, quando ocorre um problema, sabe-se imediatamente o ponto exato da ocorrência. Porém, passos muito curtos podem incomodar o programador, que tem que interromper seu fluxo de raciocínio a todo momento para mudar de janela, dar o comando para rodar os testes, ver o resultado e, só então, retomar o fio da meada. Muitas vezes, o que acontece é que ele deixa de seguir a recomendação, abrindo mão dos benefícios.

Será que o problema está no programador ou no ambiente de desenvolvimento? Neste artigo você aprenderá uma maneira de praticar TDD em JavaScript que agiliza a visualização de resultados e promove continuidade no fluxo de trabalho. Será necessário apenas salvar o arquivo editado para ver os resultados dos testes imediatamente.

Como o framework do momento para o trabalho com JavaScript é o Node.js, nesse artigo trabalharemos com ele e com o seu repositório NPM. E para nos ajudar com os testes usaremos o Gulp, que é um automatizador de tarefas.

## TDD Contínuo: responsividade

Praticar TDD Contínuo significa que você verá o resultado da execução dos testes do seu projeto sem precisar sair do ambiente de desenvolvimento e executar um comando específico. O comando será executado automaticamente assim que você salvar o arquivo que estiver editando. Configure seu ambiente usando as ferramentas descritas neste artigo e perceba a diferença em sua produtividade.

O Node.js é um ambiente de execução JavaScript que roda diretamente sobre o sistema operacional, geralmente em servidores. Ele permite executar o código JavaScript fora do navegador, ampliando a abrangência da linguagem.

O NPM (Node Package Manager) é o gerenciador de pacotes do Node. Ele facilita o download e a instalação de bibliotecas e ferramentas de terceiros. É similar ao NuGet (.NET), ao Maven (Java) e ao RubyGems (Ruby).

Já o Jasmine é um framework para execução de testes automatizados que pode ser usado em qualquer ambiente JavaScript, seja dentro ou fora do navegador. Ele permite especificar o comportamento esperado de um sistema da mesma maneira que a ferramenta rspec (Ruby).

O Gulp é uma ferramenta de automação de construção de projetos, assim como Make (C), Ant e Maven (Java) e Rake (Ruby). O Gulp possibilita escrever scripts para executar tarefas comuns no mundo JavaScript, como ofuscar e minificar arquivos, converter de SASS/LESS para CSS e converter de CoffeeScript para JavaScript. No contexto do TDD Contínuo, o Gulp é útil para observar o sistema de arquivos e disparar a execução dos testes quando ocorrerem mudanças em arquivos existentes

O gulp-jasmine é um plugin que permite iniciar o Jasmine a partir do Gulp. Geralmente, o Jasmine é usado em conjunto com o navegador e neste artigo ele rodará sobre o Node em uma janela de terminal.

Já o Browserify é um gestor de módulos para JavaScript que permite usar no navegador o mesmo padrão de definição e exportação de módulos adotado pelo Node. Isso possibilita rodar, no navegador, o código escrito originalmente para o Node, sem nenhuma modificação.

### Exemplo 1: criando um projeto do zero

Nesta seção será criado um projeto JavaScript do zero que irá demonstrar a configuração das ferramentas utilizadas para praticar o TDD Contínuo.

Vamos começar abrindo uma janela de terminal e executando os comandos descritos para instalar o Node.js e NPM, de acordo com os tipos de sistemas operacionais.

No Ubuntu ou derivados, use o seguinte código:

```
sudo apt-get install nodejs-legacy
# se nodejs-legacy não estiver disponível, troque por nodejs
sudo apt-get install npm
```
Em outros sistemas operacionais, acesse a página do Node (vide seção Links), baixe e instale a versão adequada. O NPM será instalado juntamente com o Node.

Para verificar se a instalação foi bem sucedida, execute estes comandos a seguir e verifique se aparecem os números das versões instaladas:

```
node -v
npm -v
```

Para instalar o pacote Gulp globalmente, use o código a seguir:

```
sudo npm install gulp -g
```

Se estiver no Windows, omita a palavra sudo.

Agora precisamos criar um diretório chamado exemplo1 para o novo projeto e acessá-lo. Em qualquer sistema operacional, use os seguintes comandos para isso:

```
mkdir exemplo1
cd exemplo1
```

Em seguida, inicie um novo projeto usando o NPM por meio do comando a seguir:

```
npm init
```

Esse comando cria um arquivo package.json, que é similar ao pom.xml do Maven (Java). Ele descreve os dados básicos do seu projeto e também as dependências de pacotes externos.

Será solicitado o preenchimento de vários dados, como o nome do projeto, versão, descrição e licença, mas para a finalidade deste artigo não é necessário preencher nenhum desses dados. Você pode simplesmente pressionar Enter em todas as perguntas para usar todos os valores padrão.

Instale os pacotes gulp e gulp-jasmine localmente usando o comando a seguir:

```
npm install gulp gulp-jasmine --save-dev
```

Esse comando instala as ferramentas gulp e gulp-jasmine localmente, ou seja, somente no projeto atual, em um diretório chamado node_modules. A parte --save-dev instrui o npm a atualizar o arquivo package.json, acrescentando gulp e gulp-jasmine como dependências do projeto em tempo de desenvolvimento. Isso permite baixar e instalar novamente as dependências em qualquer momento no futuro apenas executando o comando npm install. Isso é útil caso não deseje adicionar as dependências ao controle de versão. Se você usa Git, por exemplo, pode adicionar node_modules ao arquivo .gitignore, evitando versionar as dependências externas.

É necessário criar um arquivo de configuração do Gulp na raiz do projeto. Para isso, crie a pasta src com a seguinte sequência de comandos:

```
mkdir src
cd src
mkdir spec
mkdir prod
cd ..
```

### Criação do gulpfile

O arquivo que configura a execução do Gulp é chamado de gulpfile, por isso, na raiz do projeto, crie um arquivo gulpfile.js com o conteúdo mostrado na Listagem 1.

Listagem 1. Conteúdo do arquivo exemplo1/gulpfile.js

```
var gulp = require('gulp');
var jasmine = require('gulp-jasmine');

var caminhoCodigoFonte = 'src/**/*.js';

gulp.task('testar', function() {
            gulp.src(caminhoCodigoFonte)
                        .pipe(jasmine());
});

gulp.task('tdd-continuo', ['testar'], function() {
            gulp.watch(caminhoCodigoFonte, ['testar']);
});

process.on('uncaughtException', function(e) {
            console.error(e.stack);
});
```

Esse arquivo define duas tarefas para o gulp:

testar: essa tarefa executa os testes uma vez e termina;
tdd-continuo: essa tarefa executa os testes uma vez e depois observa o sistema de arquivos e re-executa os testes automaticamente a cada modificação. O trecho process.on('uncaughtException') instrui o Node.js a, ao invés de encerrar o programa na ocorrência de exceções, apenas escrevê-las na tela. Isso é útil para manter o Gulp rodando mesmo em caso de erros de compilação. Essa solução é aceitável nesse caso porque o Node está sendo usado apenas como ferramenta auxiliar de desenvolvimento. Para hospedar uma aplicação no Node em produção, esse tipo de solução não deve ser usada.

## Código-fonte do projeto

Uma vez criada a estrutura do projeto, serão escritos os arquivos JavaScript. Para este exemplo, imagine uma classe representando uma árvore, que deva oferecer cinco frutos. Primeiramente, será escrita uma especificação para árvores. Em seguida, a classe Arvore será implementada. Isso será feito em passos bem curtos, com verificação contínua para ver se o código está correto. Em uma linguagem dinâmica e interpretada como o JavaScript, isso faz uma grande diferença: não se tem à disposição um compilador capaz de apontar muitos erros de programação, como é o caso do Java ou C#. Assim, o teste automatizado passa a ser o detector desses erros.

O código de especificação (ou seja, de teste) será colocado em src/spec e o código de produção (ou seja, aquele que implementa funcionalidade) em src/prod. Abra um editor de texto e crie os arquivos ArvoreSpec.js e Arvore.js, presentes nas Listagens 2 e 3, respectivamente.

Listagem 2. Conteúdo do arquivo exemplo1/src/spec/ArvoreSpec.js

```
var Arvore = require('../prod/Arvore');

describe('Arvore', function() {
            it('deve possuir 5 frutos', function() {
                        expect(new Arvore().obterFrutos().length).toBe(5);
            });
});
```

Listagem 3. Conteúdo do arquivo exemplo1/src/prod/Arvore.js

```
function Arvore() {
}
module.exports = Arvore;
```

O arquivo ArvoreSpec.js contém a especificação da classe Arvore, enquanto que o arquivo Arvore.js define uma classe Arvore, ainda sem nenhuma propriedade ou método. A última linha contendo o trecho module.exports diz ao Node.js que aquele módulo exporta a classe Arvore. Em um projeto Node, cada arquivo JavaScript representa um módulo e cada módulo deve exportar para os outros aquilo que ele quer tornar visível. No caso, o arquivo Arvore.js exporta a classe Arvore e o arquivo ArvoreSpec.js, ao fazer a chamada require('../prod/Arvore'), obtém a classe Arvore.

## Execução do Gulp

Agora, já existe uma especificação para a classe Arvore e um esboço de sua implementação. Em um terminal, no diretório raiz do projeto, execute este comando:

```
gulp tdd-continuo
```

O único teste existente no projeto será executado e o resultado deverá ser:

TypeError: Object #<Arvore> has no method 'obterFrutos'

O Gulp continuará em execução, aguardando alterações no sistema de arquivos para tornar a executar os testes.

O próximo passo é implementar o método obterFrutos() na classe Arvore. O objetivo final é que o construtor da árvore crie um vetor de frutos e armazene-o como propriedade do objeto.

## TDD Contínuo em ação

Praticar TDD Contínuo implica em visualizar simultaneamente o código e a execução dos testes. Para isso, deixe visíveis a janela do seu editor de texto e a janela do terminal, uma ao lado da outra. Se estiver no Windows ou no Linux Mint, você pode fazer isso da seguinte maneira: clique na janela do editor e pressione TeclaWindows + SetaEsquerda; clique na janela do terminal e pressione TeclaWindows + SetaDireita. As janelas serão arranjadas lado-a-lado de maneira conveniente.

Modifique o arquivo Arvore.js para ficar conforme a Listagem 4.

Listagem 4. Código de Arvore.js após primeira modificação

```
function Arvore() {
}

Arvore.prototype.obterFrutos = function() {
            return new Array(5);
};
module.exports = Arvore;
```

Ao salvar o arquivo no editor de texto, observe no terminal que o teste é executado automaticamente, agora com sucesso. Você deverá ver a mensagem "1 spec, 0 failures”, como mostra a Figura 1.

## Ambiente de desenvolvimento com TDD Contínuo

Figura 1. Ambiente de desenvolvimento com TDD Contínuo

Pensando em TDD, apenas usamos implementação falsa, porque o método ainda não faz o trabalho completo: ele apenas cria um novo vetor temporário e o retorna. O teste passa, mas ainda falta armazenar o vetor numa propriedade do objeto. Vamos transformar essa implementação falsa em uma verdadeira.

Preencha o construtor da classe Arvore com esta linha de código e salve o arquivo:

```
this._frutos = new Array(5);
```
O teste deverá ser executado novamente e deverá continuar passando. Troque a linha return new Array(5); por esta e salve o arquivo:

```
return this.frutos;
```

O teste agora deverá falhar, pois falta o caractere '_' antes da palavra frutos. Corrija o erro trocando por this._frutos, salve o arquivo e veja o teste passar novamente.

Repare na facilidade para perceber o erro de digitação, no exato momento e local em que ocorreu. Isso permite trabalhar no projeto avaliando continuamente o resultado de cada modificação.

## Exemplo 2: adaptação de um projeto web

Agora veremos como adaptar um projeto web usando Jasmine para rodar no Node.

Uma diferença importante entre o Node e o navegador é o mecanismo de gestão de módulos. No Node atribui-se um valor a module.exports para exportar o conteúdo de um módulo e chama-se require('./meu-modulo') para importá-lo. Só será visível de fora do módulo aquilo que for atribuído a module.exports. Já em projetos web, a exportação geralmente é feita no escopo global e a importação é feita com elementos <script> nas páginas HTML. Isso traz vários problemas:

- Poluição do escopo global;
- Dependências entre os módulos ficam implícitas;
- Tendência a forte acoplamento;
- Necessidade de alterar o HTML a cada módulo criado, renomeado, movido ou removido;
- Impossibilidade de rodar o código no Node.js.

A solução para esses problemas é a adoção de um mecanismo eficiente de gestão de módulos. A versão mais nova do JavaScript (ECMAScript 6) traz uma solução nativa. Enquanto não for viável adotá-la por questões de compatibilidade, pode-se usar uma das muitas ferramentas existentes para essa finalidade. Entre as mais conhecidas estão RequireJS, Almond e Browserify. A escolhida para ser usada no artigo foi esta última, principalmente pela sua simplicidade de uso.

### Projeto tocador de música

Quando a ferramenta Jasmine é baixada a partir do site oficial (ver seção Links), o pacote zip inclui um pequeno projeto de exemplo. Esse projeto simula um aplicativo tocador de música. Existem testes para a classe Player (no arquivo PlayerSpec.js) e existem duas classes de produção (Player e Song). A estrutura de arquivos e diretórios é mostrada na Listagem 5.

Listagem 5. Estrutura de arquivos e diretórios do exemplo 2

```
exemplo2/
            lib/
                        jasmine-2.2.0/*
            spec/
                        PlayerSpec.js
                        SpecHelper.js
            src/
                        Player.js
                        Song.js
            MIT.LICENSE
            SpecRunner.html
```

SpecRunner.html é a página que carrega os módulos e executa os testes. Ao abrir esse arquivo no navegador, os testes rodam normalmente, conforme mostrado na Figura 2. Porém, se você tentar executar o módulo PlayerSpec no Node, verá que o módulo Player não terá sido carregado. É necessário modificar os módulos do projeto para ficarem compatíveis com o Node. E, para que eles continuem sendo compatíveis com o navegador, deve-se usar o Browserify.

Execução dos testes no navegador
Figura 2. Execução dos testes no navegador

Os comandos a seguir irão configurar o projeto de exemplo para rodar tanto no navegador quanto no Node, permitindo praticar TDD Contínuo, como no Exemplo 1.

Para instalar o Browserify use um terminal e execute:

```
sudo npm install -g browserify
```

Se estiver no Windows, omita a palavra sudo.

Baixe o zip do Jasmine a partir do endereço https://github.com/jasmine/jasmine/blob/master/dist/jasmine-standalone-2.2.0.zip?raw=true e extraia-o em um diretório exemplo2. Se estiver usando Linux, execute no terminal:

```
wget https://github.com/jasmine/jasmine/blob/master/dist/jasmine-standalone-2.2.0.zip?raw=true -O jasmine-standalone-2.2.0.zip
unzip jasmine-standalone-2.2.0.zip -d exemplo2
cd exemplo2
```

Altere o código-fonte para usar o estilo do Node de gestão de módulos. Abra src/Player.js num editor de texto e acrescente esta linha ao final do mesmo arquivo:

```
module.exports = Player;
```
Faça o mesmo com src/Song.js. Acrescente esta linha ao final do arquivo:

```
module.exports = Song;
```
No módulo spec/PlayerSpec.js, acrescente estas linhas no início do arquivo:

```
var Player = require('../src/Player');
var Song = require('../src/Song');
```

Crie um diretório bin na raiz do projeto, onde será gerado um script único contendo todos os módulos. Em seguida, na raiz do projeto, execute o comando browserify. Ainda no diretório raiz do projeto, execute:

```
browserify spec/SpecHelper.js spec/PlayerSpec.js -o bin/browserify-bundle.js
```

Altere SpecRunner.html para incluir o script único, assim, remova este trecho:

```
<!-- include source files here... -->
<script src="src/Player.js"></script>
<script src="src/Song.js"></script>

<!-- include spec files here... -->
<script src="spec/SpecHelper.js"></script>
<script src="spec/PlayerSpec.js"></script>
```

e acrescente este trecho no lugar:

```
<script src="bin/browserify-bundle.js"></script>
```

Teste o projeto no navegador, abrindo no navegador a página SpecRunner.html e veja que os testes devem ocorrer normalmente.

## Configurando e rodando o Gulp

Agora, para praticar o TDD Contínuo basta fazer como no exemplo anterior. No diretório raiz do projeto execute o seguinte comando:

```
npm init     # responda vazio a todas as perguntas
npm install gulp gulp-jasmine --save-dev
```

Crie um gulpfile.js, com o conteúdo mostrado na Listagem 6.

Listagem 6. Conteúdo do arquivo exemplo2/gulpfile.js

```
var gulp = require('gulp');
var jasmine = require('gulp-jasmine');

var caminhoCodigoFonte = '**/*.js';
var caminhoSpecs = 'spec/*.js';

gulp.task('testar', function() {
            gulp.src(caminhoSpecs)
                        .pipe(jasmine());
});

gulp.task('tdd-continuo', ['testar'], function() {
            gulp.watch(caminhoCodigoFonte, ['testar']);
});

process.on('uncaughtException', function(e) {
            console.error(e.stack);
});
```

Após a criação do Gulpfile, no terminal, execute, na raiz do projeto:

```
gulp tdd-continuo
```

Com isso, você deverá ver o mesmo resultado do exemplo anterior: o terminal deve mostrar a execução dos testes e aguardar por modificações no sistema de arquivos para re-executá-los.

Este artigo mostrou como configurar um ambiente de desenvolvimento JavaScript para facilitar a prática do TDD. Além do que foi mostrado, algumas melhorias são possíveis. Uma delas é o uso do pacote externo gulp-watch para detectar a criação de novos arquivos (não somente a modificação dos já existentes). Também é possível usar o pacote watchify para chamar o Browserify automaticamente a cada alteração, simplificando a execução frequente de testes no navegador.

[Artigo original](https://www.devmedia.com.br/tdd-continuo-em-javascript-com-node-js-e-gulp/32467)