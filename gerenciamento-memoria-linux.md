# Gerenciamento de Memória


## Memória Virtual

Lidar com memória física é bastante complexo, para evitar essa complexidade o conceito de memória virtual foi desenvolvido.

A memória virtual abstrai os detalhes da memória física para o software. Além disso, ela fornece um mecanismo de proteção e controle dos dados entre processos.

Quando a CPU decodifica uma instrução que lê ou escreve em memória, ela traduz o endereço virtual para um endereço físico para que o controlador de memória consiga entender.

A memória física é dividida em páginas. Cada página física pode ser mapeada como uma página virtual. Essas páginas são armazenadas numa tabela de páginas que permite a tradução de um endereço virtual, que é utilizado pelos programas, para o endereço físico.

## Huge Pages

Traduzir um endereço de memória requer diversos acessos a memória e eles são relativamente lentos comparados a velocidade da CPU. As CPUs mantém um cache dessas traduções que são chamadas Translation Lookaside Buffer (ou TLB).

## Zones

É comum que o hardware imponha restrições sobre como diferentes intervalos de memória física podem ser acessados. Em alguns casos, dispositivos não podem acessar diretamente a memória. Em outros, o tamanho da memória física extroapola o número de endereços que a memória virtual pode conter e ações especiais são necessárias para acessar certas partes da memória. O linux divide páginas de memória em zonas.
```
ZONE_DMA: contém memória que pode ser usada por dispositivo para acesso direto à memoria;
ZONE_HIGHMEM:contém memória que não é permanentemente mapeada no espaço de endereços do kernel;
ZONE_NORMAL: contém memória normal que é endereçada;
```

## Nós

NUMA é uma plataforma que possui múltiplos componentes interconectados e cada um desses podem conter zero ou mais CPUs, memória e barramento de entrada/saída. 

Na visão do Linux, NUMA é uma plataforma em que a memória é vísivel e acessível à qualquer CPU que esteja em um componente. Dependendo de quão longe a memória está do processador o tempo de acesso pode ser longo.

Cada componente é conhecido como "nó" e para cada nó o Linux cria um sub-sistema independente de gerencia de memoria e cada nó possui sua lista de zonas.

## Page Cache

A memória física é volátil e uma forma comum de alocar dados na memória é lendo o conteúdo de algum arquivo. Quando um arquivo é lido, os dados são armazenado no page cache, que são partes não utilizadas da RAM, para evitar acessos à memória nas leituras futuras.
Quando um arquivo é escrito os dados são alocados no page cache e eventualmente é armazenado no HD.


## Memória Anônima

A memória anônima ou mapeamento anônimo representa uma memória que não está ligada a um sistema de arquivos. Esses mapeamento são criados para a call stack, heap ou chamando a  system call mmap(2).

## Reclaim

Uma página física pode ser utilizada para armazenar diferentes tipos de dados. Podem ser dados internos do kernel, um buffer para acesso direto à memória, dados lidos de algum arquivo, processos do usuário etc.

Dependendo do uso da página o sistema de gerenciamento de memória do Linux trata ela de forma diferente. As páginas que podem ser desalocadas a qualquer momento são aquelas que possuem dados que já estão armazenados ou porque elas podem ser armazenadas no HD, essas páginas são chamadas de reclaimable. As mais comuns são page cache e memória anônima.

Na maioria das vezes, páginas que possuem dados do kernel e buffers de acesso direto à memória não podem ser reaproveitadas, e estão presas até que o usuário libere a página.

## Compaction

Conforme o sistema roda, a memória é alocada e desalocada e se torna fragmentada. Apesar de utilizarmos memória virtual, é possível representar páginas físicas dispersas como páginas virtuais praticamente contíguas. Memory compaction resolve esse problema.

## OOM Killer

É possível que a memória se esgote e o kernel não terá memória suficiente para continuar operando. Para salvar o resto do sistema, ele chama o OOM Killer.

O OOM Killer seleciona um processo para sacrificar pelo bem da saúde do sistema. Esse processo será sacrificado na esperança que depois o sistema terá memória o bastante para operar normalmente.

## Referência
[Kernel](https://www.kernel.org/doc/html/latest/admin-guide/mm/index.html)
