---
description: Os cinco princípios básicos da programação orientada a objetos
---

# SOLID \(draft\)

SOLID é um conjunto de 5 princípios aplicados a programação orientada à objetos que tem por objetivo facilitar o desenvolvimento de software tornando o  código-fonte mais fácil de manter e estender.

## Single Responsibility Principle \(SRP\)

{% hint style="success" %}
**Uma classe deve ter um, e somente um, motivo para mudar.**
{% endhint %}

O princípio da responsabilidade única defende que uma classe deve ser especializada em apenas uma tarefa ou funcionalidade.

> Quando estamos aprendendo programação orientada a objetos, sem sabermos, damos a uma classe mais de uma responsabilidade e acabamos criando classes que fazem de tudo — _God Class\*_. Num primeiro momento isso pode parecer eficiente, mas como as responsabilidades acabam se misturando, quando há necessidade de realizar alterações nessa classe, será difícil modificar uma dessas responsabilidades sem comprometer as outras. Toda alteração acaba sendo introduzida com um certo nível de incerteza em nosso sistema — principalmente se não existirem testes automatizados!
>
> ROBERTO, 2019.

A violação do SRP gera problemas ao processo de desenvolvimento de software como por exemplo:

* Falta de coesão: uma classe não deve assumir responsabilidades que não são suas.
* Alto acoplamento: quanto mais responsabilidades a classe possuir, maior o nível de dependências, tornando o a manutenção do software  mais difícil.
* Testes automatizados mais complexos: quanto maior o nível de dependências, mais difícil será escrever testes automatizados, principalmente testes unitários.
* Reaproveitamento de código: alta dependência dificulta, ou até mesmo inviabiliza o reaproveitamento.

{% tabs %}
{% tab title="Errado" %}
```java
class Aluno {
    private String nome;
    private Double nota1;
    private Double nota2;
    
    public double calcularMedia() {/*...*/}
    public List<Aluno> findAll() {/*...*/}
    public void save(Aluno aluno) {/*...*/}
    public void delete(Aluno aluno) {/*...*/}
}
```
{% endtab %}

{% tab title="Certo" %}
```java
class Aluno {
    private String nome;
    private Double nota1;
    private Double nota2;
    
    public double calcularMedia() {/*...*/}
}

class AlunoRepository {
    public List<Aluno> findAll() {/*...*/}
    public void save(Aluno aluno) {/*...*/}
    public void delete(Aluno aluno) {/*...*/}
}
```
{% endtab %}
{% endtabs %}

No exemplo **errado**, a classe Aluno viola o SRP pois realiza dois tipos de tarefas. Ela realiza o cálculo da média do aluno e, também, manipula os dados buscando, salvando e alterando os registros.

No exemplo **correto**, a classe Aluno trata apenas do cálculo da média enquanto a classe AlunoRepository cuida da manipulação dos dados.

Também é possível aplicar o SRP a nível de métodos, onde um método deve ser responsável por apenas uma tarefa.

{% tabs %}
{% tab title="Errado" %}
```java
/* Adaptado de PLENIK, 2019 */
public class Cliente {/*...*/}
public class ClienteRepository {/*...*/}

public class Email {
    public void enviarEmailClientes(List<Integer> clientes) {
        for(int i=0; i<clientes.size(); i++) {
            Cliente cliente = ClienteRepository.findById(clientes.get(i));
            if(cliente.isActive()) {
                email(cliente);
            }
        }
    }
    public enviarEmail(Cliente cliente) {/*...*/}
}
```
{% endtab %}

{% tab title="Certo" %}
```java
/* Adaptado de PLENIK, 2019 */
public class Cliente {/*...*/}
public class ClienteRepository {/*...*/}

public class Email {
    public void enviarEmailClientes(List<Integer> clientes) {
        List<Client> clientesAtivos = ClienteRepository.findAllActive(clientes);
        for(int i=0; i<clientesAtivos.size(); i++) {
            enviarEmail(clientesAtivos.get(i));
        }
    }
    public enviarEmail(Cliente cliente) {/*...*/}
}
```
{% endtab %}
{% endtabs %}

Neste exemplo a classe Email está enviando e-mails para todos os clientes ativos. Na implementação correta a atividade de identificar se o cliente é ativo foi movida para a classe ClienteRepository, deixando a implementação de envio apenas com a responsabilidade de enviar os emails.

{% hint style="warning" %}
**EXERCÍCIO**: Existe um jeito melhor de implementar os exemplos acima usando Java 8. Para simplificar, faça a implementação considerando que o ClienteRepository retorna uma List com Clientes fixos e que o método enviarEmail apenas imprime o conteúdo do objeto cliente.
{% endhint %}

## Open-Closed Principle \(OCP\)

{% hint style="success" %}
**Objetos ou entidades devem estar abertos para extensão, mas fechados para modificação.**
{% endhint %}

O princípio do aberto-fechado define que na necessidade de novos comportamentos é necessário que o objeto ou entidade seja estendido ao invés de alterado.

## Liskov Substitution Principle \(LSP\)

{% hint style="success" %}
Uma classe derivada deve ser substituível por sua classe base.
{% endhint %}

O princípio da substituição de Liskov defende que havendo um ancestral ou superclasse na árvore de herança da classe especializada, deve ser possível que o software use o ancestral como referência por meio de polimorfismo.

> \[...\] se S é um subtipo de T, então os objetos do tipo T, em um programa, podem ser substituídos pelos objetos de tipo S sem que seja necessário alterar as propriedades deste programa. \(WIKIPEDIA, 2019\)

{% tabs %}
{% tab title="Exemplo" %}
```java
public class Computador {
    public void desligar() {
        System.out.println("Desligando");
    }
}
public class Laptop extends Computador {
    public void desligar() {
        System.out.println("Hibernando");
    }
}
public class Exemplo {
    public static void main(String args[]) {
        Computador recepcao = new Computador();
        Laptop salaReuniao = new Laptop();
        
        desligarComputador(recepcao);
        desligarComputador(salaReuniao);
    }
    public void desligarComputador(Computador equipamento) {
        equipamento.desligar();
    }
}
```
{% endtab %}
{% endtabs %}

Em Java a implementação que melhor ilustra o LSP talvez seja a API de [Collections](http://bit.ly/2RdF1vy).

## Interface Segregation Principle \(ISP\)

{% hint style="success" %}
Uma classe não deve ser forçada a implementar interfaces e métodos que não irão utilizar.
{% endhint %}

O princípio da segregação de interfaces define que interfaces devem ser específicas ao invés de genéricas, ou seja, mais especializadas. Isso permite que ao criar classes que irão implementar estas interfaces, apenas os métodos necessários a classe sejam implementados.

## Dependency Inversion Principle \(DIP\)

{% hint style="success" %}
Um módulo de alto nível não deve depender de módulos de baixo nível, ambos devem depender da abstração.
{% endhint %}

O princípio da inversão de dependência define que módulos que possuem dependência entre si devem optar por usar abstrações ou invés das implementações específicas.

{% hint style="warning" %}
ATENÇÃO: inversão de dependência \(princípio\) é diferente de injeção de dependência \(padrão de projeto\).
{% endhint %}

## Bibliografia

PLENIK, Piotr. **Clean Code PHP**. GitHub. Disponível em: [http://bit.ly/3asMyOC](http://bit.ly/3asMyOC). Acesso em: 15 de Janeiro de 2019.

ROBERTO, João. **O que é SOLID**: O guia completo para você entender os 5 princípios da POO. Medium, 2019. Disponível em: [http://bit.ly/36imUJ4](http://bit.ly/36imUJ4). Acesso em: 6 de Janeiro de 2019.

WIKIPEDIA. Princípio da substituição de Liskov. Wikipedia, 2020. Disponível em: [http://bit.ly/38pn56X](http://bit.ly/38pn56X). Acesso em: 20 de Janeiro de 2020.

