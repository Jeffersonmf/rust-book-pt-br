# Ponteiros Inteligentes (Smart Pointers)

Um _ponteiro_ é um conceito geral para uma variável que contém um endereço de
memória. Esse endereço se refere a, ou "aponta para", algum outro dado. O tipo
mais comum de ponteiro em Rust é a referência, sobre a qual você aprendeu no
Capítulo 4. Referências são indicadas pelo símbolo `&`, e pegam emprestado o
valor para o qual apontam. Elas não têm nenhuma outra habilidade senão
referir-se a dados. Além disso, elas não têm nenhum custo adicional e são o tipo
de ponteiro que usamos com maior frequência.

_Ponteiros inteligentes_ (_smart pointers_), por outro lado, são estruturas de
dados que agem como um ponteiro mas também têm metadados e habilidades
adicionais. O conceito de ponteiros inteligentes não é exclusivo do Rust: ele
teve origem no C++ e também existe em outras linguagens. No Rust, os diferentes
ponteiros inteligentes definidos na biblioteca padrão proveem funcionalidades
além daquelas providas pelas referências. Um exemplo que vamos explorar neste
capítulo é o tipo de ponteiro inteligente de _contagem de referências_
(_reference counting_). Esse ponteiro lhe permite ter múltiplos possuidores de
um dado. Ele mantém registro do número de possuidores e, quando não resta
nenhum, cuida de limpar o dado.

Em Rust, onde temos os conceitos de posse (_ownership_) e empréstimo
(_borrowing_), uma diferença adicional entre referências e ponteiros
inteligentes é que referências são ponteiros que apenas _pegam emprestados_ os
dados; em contraste, em muitos casos, ponteiros inteligentes _têm posse_ dos
dados aos quais apontam.

Nós já encontramos alguns ponteiros inteligentes neste livro, como `String` e
`Vec<T>` no Capítulo 8, apesar de não os termos chamado de ponteiros
inteligentes naquele momento. Ambos esses tipos contam como ponteiros
inteligentes porque eles têm posse de uma parte da memória e permitem que você a
manipule. Eles também têm metadados (como sua capacidade) e habilidades extras
ou garantias (como a garantia que `String` dá de que seus dados serão sempre
UTF-8 válido).

Ponteiros inteligentes normalmente são implementados usando structs. A
característica que distingue um ponteiro inteligente de uma struct qualquer é
que ele implementa as traits `Deref` e `Drop`. A trait `Deref` permite que uma
instância da struct do ponteiro inteligente se comporte como uma referência.
Assim podemos escrever código que funcione tanto com referências quanto com
ponteiros inteligentes. A trait `Drop` nos permite personalizar o código que é
executado quando uma instância do smart pointer sai de escopo. Neste capítulo,
discutiremos ambas as traits e demonstraremos porque são importantes para
ponteiros inteligentes.

Dado que os ponteiros inteligentes são um padrão de projeto (_design pattern_)
usado com frequência em Rust, este capítulo não irá cobrir todo ponteiro
inteligente que existe. Muitas bibliotecas têm seus próprios ponteiros
inteligentes, e você pode até mesmo criar seus próprios. Nós vamos cobrir os
ponteiros inteligentes mais comuns na biblioteca padrão:

- `Box<T>`, para alocar valores no heap
- `Rc<T>`, um tipo com contagem de referências que permite posse múltipla
- `Ref<T>` e `RefMut<T>`, acessados através de `RefCell<T>`, um tipo que aplica
  as regras de empréstimo em tempo de execução em vez de em tempo de compilação

Além disso, vamos cobrir a pattern de _mutabilidade interior_ (_interior
mutability_), onde um tipo imutável expõe uma API para modificar um valor
interno. Também vamos discutir _ciclos de referências_: como eles podem vazar
memória e como evitá-los.

Mergulhemos!
