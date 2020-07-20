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

Mas, qual seria a melhor maneira possível? E é justamente por isso que precisamos ter métricas e indicadores, além de ter rastreabilidade sobre eles. Se não, como ter certeza de que tivemos algum avanço?

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

<<<<<<< Updated upstream
> Facilitar o uso de HOCs, estado, ciclo de vida.
=======
> HOCs, estado, ciclo de vida.
>>>>>>> Stashed changes

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

O hook useMemo é muito semelhante ao useCallback, a diferença é que o useMemo permite a memoização de qualquer tipo de valor, não apenas funções. O hook é usado para memoizar o retorno de uma função.

```JavaScript
useCallback(fn, deps) é equivalente a useMemo(() => fn, deps).
```

Podemos também, por exemplo, ter uma lista de erros memoizada, que será alterada mediante X condição (um state de error por exemplo)

> Exemplo do useMemo: https://codesandbox.io/s/primes-use-memo-xusrv

## Uncontrolled Components

A forma tradicional de se construir formulários com React é guardar em um estado os valores dos componentes, com um handler para o evento onChange.

Existem algumas vantagens de se usar componentes controlados, pelo fato de termos acesso imediato aos valores dos campos, como por exemplo validação instantânea dos campos e desabilitar condicionalmente o botão de submit.

No caso dos componentes não controlados, é o próprio DOM o responsável por gerenciar o valor do input, o React passa a ter acesso a esse input através de uma referência.

Dependendo da situação é mais vantajoso se utilizar componentes não controlados. Imagine um cenário onde temos uma lista com 1000 inputs (uma lista com checkboxes), se guardarmos o value em um estado, cada vez que eu alterar um desses checkboxes, a lista inteira será renderizada novamente. Se multiplicarmos isso por um grande número de componentes, o lag será instaurado!

> Roteiro: conceituar uncontrolled components, diferença para controlled components, exemplificar.

## Listas Virtualizadas

Virtualização é um processo, onde através do software é criada uma representação de aplicativos, servidores, sistemas operacionais, redes e armazenamento.

É uma maneira de reduzir custos de TI ao mesmo tempo em que aumenta a eficiência e agilidade, que estão ligados diretamente à performance.

As listas virtualizadas permitem que tenhamos infinitos elementos em um lista, e apenas o que é exibido na tela será de fato renderizado no DOM.

Essa foto representa bem o que ocorre em um cenário convencional. Temos uma lista, renderizada por inteiro no DOM, porém apenas uma parte dela é vista pelo usuário.

Na maioria dos casos não há problemas em se trabalhar dessa forma, mas quando falamos de uma grande quantidade de elementos, como por exemplo o feed do twitter, um histórico de chat ou uma lista com infinite scrolling, podemos ter problemas principalmente em devices low-mid-end,o que pode afetar a experiência sobre a plataforma.

Alguns problemas:

- Aumento do tempo de renderização.
- Queda de fps
- Alto consumo de ram

![Non Virtualized](https://i.imgur.com/MlCPfl1.png)

Essa próxima foto ilustra o comportamento de uma lista virtualizada, diferentemente do outro exemplo, aqui apenas os elementos visíveis são renderizados. Quando damos scroll no container, os elementos são substituídos programaticamente.

![Virtualized](https://i.imgur.com/FTMppSV.png)

Existem algumas maneiras de se implementar uma lista virtualizada, podemos posicionar os elementos de forma absoluta no container, podemos posicionar de acordo com a altura dos itens, empilhar no container e etc...

> Ver intersection observer

> **Viewport**: porção visível da lista, da tela, do container...

> Roteiro: o grande "tcharam" da apresentação. Conceituar, exemplificar e mostrar o componente.

## Otimizações

Google Analytics

> Roteiro: dar alguns exemplos de ferramentas que podem ser utilizadas para mensurar a performance, assim como dicas de otimização.

## ETC...

![Dan Abramov Tweet](https://i.imgur.com/4UKcuRP.png)

[React shallow equality function](https://github.com/facebook/react/blob/v16.8.6/packages/shared/shallowEqual.js)

> Falar do Profiler do React e sua api de tracing (experimental), o quanto pode ser interessante para mensurar timings (o que o Wesley está fazendo através do GA)

> Falar sobre o unused webpack plugin
