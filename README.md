# React: Aplicações mais performáticas, menos problemáticas.

O intuito da apresentação é explicar alguns conceitos e como podemos mensurar e identificar problemas de performance.

Posso dividir entre 3 objetivos:

1. Fixar meus conhecimentos, pois a melhor maneira de assimiliar informação nova é explicando para alguém, ou alguma coisa.
2. Trazer um conteúdo introdutório-básico-intermediário que possa agregar aos novos integrantes da Hi (estagiários e fronts mais juniores).
3. Abrir uma discussão sobre o tema para melhorarmos nossa qualidade não só como desenvolvedores mas também dos produtos em que nos envolvemos.

Basicamente iremos falar sobre:

1. APIs do react:
   1.1. useCallback;
   1.2. useMemo;
   1.3. useLayoutEffect;
   1.4. React.memo;
   1.5. Suspense;
   ⋅⋅1.5.1. Lazy
   ⋅⋅1.5.2. Data fetching
2. Uncontrolled Components
3. Listas Virtualizadas

> O conteúdo visto durante a apresentação é resultado dos estudos do acompanhamento de carreira.

## O que é performance (desempenho)?

> Performance: sinônimo de desempenho. Palavra inglesa, com origem francesa, usada para referenciar artistas (dança e etc)

Se pesquisarmos no google o significado da palavra desempenho, veremos algumas definições interessantes:

1. Cumprimento de obrigação ou de promessa,
2. Modo como alguém ou alguma coisa se comporta tendo em conta sua eficiência, seu rendimento: o desempenho de uma gestão, de um cantor ou atleta. (_aparentemente é a definição do dicionário, espero que seja mesmo_)

Vemos que o termo é um tanto subjetivo e aberto para interpretações, pode significar muitas coisas, como pode significar nada, dependendo do contexto em que for aplicado.

Pensando no item 1: De forma simplificada, o papel do Inbox é prover um meio para que seus usuários prestem suporte aos consumidores finais (é um produto B2B, dã). Essa é a obrigação, ou promessa do produto, e é claro que queremos fazer isso da **melhor** maneira possível, certo?

Mas, qual seria a melhor maneira possível? E é justamente por isso que precisamos ter métricas e indicadores, para sabermos o rendimento, além de ter rastreabilidade sobre eles. Se não, como ter certeza de que tivemos algum avanço?

Durante muito tempo, eu, como desenvolvedor, sempre quis codificar da melhor maneira possível, desde o princípio da aplicação. Sempre quis que tudo tivesse um desempenho incrível assim que saísse do forno. E quem nunca? Mas, justamente pela questão das métricas, dos indicadores e da rastreabilidade, não se pode colocar a carroça na frente dos bois.

A otimização precoce também é um grande problema. Como otimizar o que não sabemos que precisa ser otimizado? O React é uma lib extremamente performática, precisamos pensar também, se o custo de se querer fazer alguma otimização será maior do que o benefício que ela trará. **E isso também precisa ser mensurado**.

Isso eu também descobri com o tempo, apesar de ainda me afetar um pouco:

> A quantidade de vezes que um componente renderiza não é um problema!

Ciclo de renderização, commits, updates, como o React enxerga e trata um elemento do DOM (que na verdade é um objeto \o/) são assuntos que dariam algumas horas de discussão, como não temos todo esse tempo disponível, vou deixar alguns links do nosso querido KCD, onde ele explica de forma mais aprofundada.

https://kentcdodds.com/blog/fix-the-slow-render-before-you-fix-the-re-render
https://github.com/kentcdodds/react-performance (_nos exercícios desse workshop tem vários exemplos e explicações sobre o tema_)

Nos meus estudos, cheguei a um conceito de perfomance fácil de classificar, entender, e mensurar, que é dividido em dois tipos: **objetiva** e **subjetiva**

### Objetiva (mensurável)

A performance objetiva é todo dado que pode ser mensurado de forma clara e precisa:

1. Tamanho do bundle;
2. Tempo de processamento da requisição;
3. Tempo de processamento das ações em massa;
4. Quantidade de itens renderizados em uma lista;
5. TTFB - Time to first byte (quanto tempo demora para que o primeiro byte seja baixado pelo navegador)
6. TTR - Time to render (quanto tempo o browser demora pra renderizar a aplicação)

### Subjetiva (perceptível)

A performance subjetiva depende da percepção do usuário, que baseado na usabilidade do sistema, terá a impressão de que a aplicação é mais ou menos performática. Nem sempre é algo que podemos mensurar:

1. Frames por segundo ao arrastar um item na tela;
2. Estado de carregamento da aplicação. Se carrega tudo de uma vez, ou por partes.
3. Quanto tempo a tela fica no estado de carregando

https://reactjs.org/docs/concurrent-mode-patterns.html#the-three-steps

## APIs do React

Os react hooks são uma "nova funcionalidade" incluída na versão 16.8 do react. Eles permitem que a gente tenha acesso ao estado e ao ciclo de vida do componente, sem que seja preciso utilizar uma classe, reduzindo a verbosidade e complexidade do código.

> Facilitar o uso de HOCs, estado, ciclo de vida.

https://kentcdodds.com/blog/usememo-and-usecallback

### useCallback

O hook useCallback recebe uma função e um array de dependências. O retorno é uma versão memoizada da função, que só vai ser alterada caso alguma dependência do array mude.

```JavaScript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

É importante notar que uma função memoizada também irá guardar as referências de todas as dependências, o que significa que podemos ter valores desatualizados se eles não forem referenciados no array de dependências. (exemplo)

É interessante usar esse hook quando a função é uma dependência de algum componente (pra manter o shouldComponentUpdate ou replicar seu funcionamento), ou de algum hook, como por exemplo o useEffect, pois o problema que o useCallback resolve é justamente a igualdade de referência das funções e objetos.

> Exemplo do useCallback: https://codesandbox.io/s/simple-callback-9qllj

https://kentcdodds.com/blog/usememo-and-usecallback
https://dmitripavlutin.com/dont-overuse-react-usecallback/
https://www.reddit.com/r/reactjs/comments/efjgfc/should_i_use_usecallback_in_every_function/

### useMemo

O hook useMemo é muito semelhante ao useCallback, ambos possuem uma assinatura similar, a diferença é que o useMemo permite a memoização de qualquer tipo de valor, não apenas funções. O hook é usado para memoizar o retorno de uma função.

```JavaScript
useCallback(fn, deps) é equivalente a useMemo(() => fn, deps).
```

Podemos também, por exemplo, ter uma lista de erros memoizada, que será alterada mediante X condição (um state de error por exemplo)

> Exemplo do useMemo: https://codesandbox.io/s/primes-use-memo-xusrv

### useLayoutEffect

O hook useLayoutEffect, tem a mesma assinatura do useEffect, a diferença é que esse hook é executado de forma síncrona, após as mutações no DOM ocorrerem, porém antes do browser efetuar o paint na tela.

Quando é interessante usar o useLayoutEffect? Saberemos quando for necessário só de olhar pro componente visualmente, e isso aconteceu na história do e-mail com cópia/oculta, pois os e-mails precisavam ser persistidos caso o usuário alternasse entre nota privada e comentário público:

![Inbox GIF](https://i.imgur.com/EpUKfdp.gif)

Como o useEffect é executado de forma assíncrona e após o browser renderizar as mudanças, ocorria um "flick" no componente, pois é preciso checar se existem e-mails salvos para renderizar nos campos, e isso faz com que o componente seja atualizado logo após ser renderizado.

É preciso tomar cuidado ao utilizar esse hook, pois como ele executa de maneira síncrona, o browser fica bloqueado até o hook terminar sua execução, o que também pode causar problemas de performance

> Exemplo do useLayoutEffect: https://codesandbox.io/s/use-layout-effect-gp14t

https://kentcdodds.com/blog/useeffect-vs-uselayouteffect

### React.memo

O conceito do React.memo é semelhante aos hooks useCallback e useMemo: **memoização**, é utilizado para memoizar componentes baseado nas suas propriedades, ele veio para substituir o shouldComponentUpdate. Por padrão, o memo faz apenas um shallow compare de objetos complexos, porém é possível passar como segundo parâmetro, uma função que recebe como parâmetro as propriedades anteriores e as atuais, e então podemos fazer uma verificação personalizada. O retorno dessa função é true caso as propriedades sejam iguais, o que significa que o componente não será renderizado novamente, e false, caso sejam diferentes, o que irá fazer com que o componente renderize.

> O shallow compare apenas verifica se as chaves do objeto possuem valores estritamente iguais, ou seja {} === {} = false

Geralmente usamos o React.memo em conjunto com o useCallback/useMemo para evitar que um componente renderize se suas propriedades não forem alteradas, quando por exemplo as propriedades/estado do pai são alterados.

Importante mencionar que apesar de parecer uma solução milagrosa, não é sempre que deve ser aplicada! Como falei anteriormente, toda otimização tem um custo. E se o custo for maior que o benefício?

- Quando não usar o React.memo?

Se o componente atualiza frequentemente com novas props, ou se **o custo da memoização não paga o custo da renderização**, será desnecessário utilizar o React.memo.

![Dan Abramov Tweet](https://i.imgur.com/4UKcuRP.png)

https://dmitripavlutin.com/use-react-memo-wisely/
https://github.com/facebook/react/issues/14463
https://github.com/facebook/react/blob/master/packages/shared/shallowEqual.js
https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Object/is

> Importante notar que o React.memo memoiza apenas as propriedades, e não o estado/context do componente.

> Exemplo do React.memo: https://codesandbox.io/s/simple-callback-memo-ig88u

### Suspense

O Suspense é um HOC que nos permite suspender (Suspense 🥁) a renderização do componente até que uma condição seja atingida. Enquanto o componente está suspenso, um fallback (contingência) é renderizado, que pode ser um spinner, um skeleton ou qualquer coisa. Atualmente o único caso de uso do Suspense é para fazer lazy load dos componentes (code splitting).

```JavaScript
const AuthApp = React.lazy(() => import("./auth-app"));
```

O Suspense veio para melhorar a experiência tanto do usuário quanto do desenvolvedor, pois nos ajuda a orquestrar mais facilmente os estados de loading da UI. Será amplamente aplicado no modo concorrente (concurrent) do React, que ainda está em fase experimental.

#### O que é code splitting?

É uma técnica utilizada para diminuir o tamanho do bundle JavaScript da aplicação, criando múltiplos chunks ao invés de um único grande arquivo.

A proposta principal do code splitting é tornar o loading inicial da aplicação mais rápido, pois o browser não precisa baixar todo o JavaScript para renderizar.

Podemos ver na prática o resultado, utilizando code splitting o bundle é dividido em alguns chunks dependendo da quantidade de lazy imports efetuados.

![Splitted](https://i.imgur.com/Jo97QjQ.png)

Já nesse print, vemos que o bundle se concentra no arquivo main.chunk.js

![Non Splitted](https://imgur.com/Y8rfAWI.png)

https://dev.to/thekashey/code-splitting-what-when-and-why-59op
https://itnext.io/what-the-heck-is-this-in-react-suspense-c5e641e487a
https://medium.com/hackernoon/lazy-loading-and-preloading-components-in-react-16-6-804de091c82d

#### Suspense for data fetching

Como dito anteriormente, o único caso de uso do Suspense atualmente é para lazy load de componentes, porém, o modo concurrent do React introduziu uma feature chamada Suspense for Data Fetching, que permite suspender o componente para qualquer tipo de dado que seja consultado de forma assíncrona.

> Explicar resumidamente o concurrent mode, que uma renderização não é "interrompível" atualmente e etc...

Importante notarmos que o Suspense não é uma biblioteca de data fetching, e sim um mecanismo que nos permite comunicar para o React que o dado que determinado componente precisa ainda não está disponível (exemplo da lista de tickets).

O Facebook mesmo utiliza o Relay (lib para fetching de apis GraphQL) e sua integração com o Suspense.

A longo prazo o objetivo é que o Suspense seja a maneira principal de se consumir dados assíncronos, independente da origem desse dado.

De forma simplificada, existiam 2 abordagens para data fetching antes do Suspense:

- Fetch-on-render: O componente renderiza, e então através do ciclo de vida, requisições são efetuadas e o componente é atualizado;
- Fetch-then-render: A requisição é efetuada e só após sua conclusão é que o componente é renderizado;

Com o Suspense, agora temos o render-as-you-fetch: o componente é renderizado enquanto a requisição é efetuada. Conforme os dados chegam, o React vai renderizando o componente até que todos os dados estejam disponíveis.

Podemos falar muito sobre o Suspense, mas para não estender, vou deixar o link da própria documentação, que explica com detalhes todo o funcionamento do Suspense no modo concurrent

> Exemplo legal: https://codesandbox.io/s/condescending-shape-s6694

https://reactjs.org/docs/concurrent-mode-patterns.html
https://reactjs.org/docs/concurrent-mode-suspense.html

## Uncontrolled Components

A forma tradicional de se construir formulários com React é guardar em um estado os valores dos componentes, com um handler para o evento onChange.

Existem algumas vantagens de se usar componentes controlados, pelo fato de termos acesso imediato aos valores dos campos, como por exemplo validação instantânea dos campos e desabilitar condicionalmente o botão de submit.

No caso dos componentes não controlados, é o próprio DOM o responsável por gerenciar o valor do input, o React passa a ter acesso a esse input através de uma referência.

Dependendo da situação é mais vantajoso se utilizar componentes não controlados. Imagine um cenário onde temos uma lista com 1000 inputs (uma lista com checkboxes), se guardarmos o value em um estado, cada vez que eu alterar um desses checkboxes, a lista inteira será renderizada novamente. Se multiplicarmos isso por um grande número de componentes, o lag será instaurado! Também se aplica caso tenhamos um formulário muito grande.

Um exemplo prático é o próprio formulário de criação de tickets do Inbox, podemos perceber visualmente o impacto que usar componentes controlados pode causar na nossa aplicação. Iniciei uma sessão de profiling da aba Performance, do Chrome, e digitei caracteres aleatório, de maneira rápida. O delay é tão perceptível que a tela chega a congelar devido a queda FPS, pois a cada letra digitada, **TODO** o componente é atualizado.

Podemos observar no gráfico: as ondas em amarelo se referem ao uso da CPU durante a execução de scripts, e as ondas verdes são a quantidade de FPS.

Não estou dizendo que usar componentes controlados é ruim, é apenas um exemplo para ilustrar o quanto é importante o custo de renderização do componente.

![Novo Ticket Modal](https://imgur.com/yl7zxFZ.png)

> Exemplo Uncontrolled-Controlled: https://codesandbox.io/s/controlled-uncontrolled-n71lw

## Listas Virtualizadas

Virtualização é um processo, onde através do software é criada uma representação de aplicativos, servidores, sistemas operacionais, redes e armazenamento.

É uma maneira de reduzir custos de TI ao mesmo tempo em que aumenta a eficiência e agilidade, que estão ligados diretamente à performance.

As listas virtualizadas permitem que tenhamos infinitos elementos em uma lista, e apenas o que é exibido na tela será de fato renderizado no DOM.

Na maioria dos casos não há problemas em se trabalhar dessa forma, mas quando falamos de uma grande quantidade de elementos, como por exemplo o feed do twitter, um histórico de chat ou uma lista com infinite scrolling, podemos ter problemas principalmente em devices low-mid-end,o que pode afetar a experiência sobre a plataforma.

Alguns problemas:

- Aumento do tempo de renderização.
- Queda de fps
- Alto consumo de recursos do dispositivo

Esse rascunho representa uma lista não virtualizada, temos a viewport (área da lista visível) na página, e os itens laranjas representam os itens da lista, temos apenas 5 itens visíveis pelo usuário, mas nesse modelo todos os itens são renderizados. Agora imaginem 10000 itens sendo renderizados no DOM, e apenas 5 deles estão visíveis

![Non Virtualized](https://i.imgur.com/MlCPfl1.png)

Essa próxima foto ilustra o comportamento de uma lista virtualizada, diferentemente do outro exemplo, aqui apenas os elementos visíveis são renderizados. Quando damos scroll no container, os elementos são substituídos programaticamente.

![Virtualized](https://i.imgur.com/FTMppSV.png)

Existem algumas maneiras de se implementar uma lista virtualizada, podemos posicionar os elementos de forma absoluta no container, podemos posicionar de acordo com a altura dos itens, empilhar no container e etc...

Preparei um exemplo básico que exemplifica bem a diferença entre essas duas abordagens. Temos um array com 20000 elementos que serão renderizados nas duas listas. Na primeira, usamos a abordagem convencional, de percorrer o array e renderizar os itens. Podemos notar que ao clicar para exibir a lista, demora em torno de 5 segundos para os itens serem renderizados, e isso pode ser visualizado nesse print do Profiler do Chrome.

Se irmos um pouco mais a fundo nesse gráfico, vemos que existem 2 pontos de atenção: a thread ficou travada 5 segundos na execução do script, nesse meio tempo a tela ficou congelada (FPS a 0), também temos um tempo de 300ms pós processamento para renderizar todos os itens no DOM.

![Profiler Non](https://imgur.com/9PVYfT1.png)

Na segunda lista utilizaremos o componente de lista virtualizada que eu criei, podemos notar que o tempo de renderização é quase instantâneo.

Se inspecionarmos o container da lista, notamos que apesar do array conter 20000 itens, apenas os itens visíveis e o range de overscan (vou explicar mais a frente) são renderizados. Conforme efetuamos o scroll na listagem, os itens são substituídos pelos seguintes, ou anteriores.

Se voltarmos ao gráfico do Profiler, a diferença é drástica. Temos apenas 100ms de scripting e 5ms de renderização. Se tirarmos a medição de Idle de ambos os gráficos, a lista virtualizada consumiu apenas 2% do tempo em comparação à lista convencional.

**A lista virtualizada foi 42 vezes mais performática**

Isso significa:

- Menos recursos desperdiçados
- Usuário feliz
- Empresa feliz
- Desenvolvedor feliz

😁

![Profiler Virtualized](https://imgur.com/2ogYEqx.png)

> Explicar um tanto superficialmente... Container dos scrolls envolvendo a lista, altura do item, array, ref do container pai, função pra renderizar os itens, função pra pegar os itens do array

> Exemplo: https://codesandbox.io/s/virtualized-list-5o4br

> Ver intersection observer

> **Viewport**: porção visível da lista, da tela, do container...

## Otimizações

Google Analytics

https://www.npmjs.com/package/unused-files-webpack-plugin
https://www.npmjs.com/package/unused-webpack-plugin

https://reactjs.org/docs/profiler.html

> Roteiro: dar alguns exemplos de ferramentas que podem ser utilizadas para mensurar a performance, assim como dicas de otimização.

> Falar do Profiler do React e sua api de tracing (experimental), o quanto pode ser interessante para mensurar timings (o que o Wesley está fazendo através do GA)

## ETC...

> Bonus: optimistic UI
