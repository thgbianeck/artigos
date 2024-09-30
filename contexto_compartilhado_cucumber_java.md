# Guia Completo: Implementando Contexto Compartilhado em Testes Cucumber com Java

Por Thiago Moreira Bianeck

## Introdução

Neste artigo, vamos explorar passo a passo como implementar um contexto compartilhado em testes de aceitação usando Cucumber com Java. Esta abordagem é especialmente útil para gerenciar o estado entre diferentes etapas (steps) de um cenário de teste. Vamos construir um projeto do zero, explicando cada etapa detalhadamente.

## Pré-requisitos

Antes de começarmos, certifique-se de ter instalado em seu computador:

1. Java Development Kit (JDK) - versão 11 ou superior
2. Maven - para gerenciamento de dependências e build do projeto
3. Um IDE de sua preferência (recomendamos IntelliJ IDEA ou Eclipse)

## Passo 1: Configuração do Projeto

Vamos começar criando um novo projeto Maven do zero.

1. Abra um terminal ou prompt de comando.
2. Navegue até o diretório onde deseja criar o projeto.
3. Execute o seguinte comando para criar um novo projeto Maven:

```bash
mvn archetype:generate -DgroupId=com.exemplo -DartifactId=cucumber-poc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Este comando cria uma estrutura básica de projeto Maven.

4. Navegue para o diretório recém-criado:

```bash
cd cucumber-poc
```

5. Abra o arquivo `pom.xml` em um editor de texto. Este arquivo contém as configurações do projeto Maven.
6. Substitua o conteúdo do `pom.xml` pelo seguinte:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>

    <groupId>com.exemplo</groupId>
    <artifactId>cucumber-poc</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-java</artifactId>
            <version>6.10.4</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-junit</artifactId>
            <version>6.10.4</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M5</version>
            </plugin>
        </plugins>
    </build>

</project>
```

    Este arquivo POM configura as dependências necessárias (Cucumber e JUnit) e define as configurações básicas do projeto.

## Passo 2: Estrutura de Diretórios

Agora, vamos criar a estrutura de diretórios necessária para nosso projeto:

1. No diretório raiz do projeto, crie as seguintes pastas:

```bash
mkdir -p src/main/java/com/exemplo
mkdir -p src/test/java/com/exemplo
mkdir -p src/test/resources/com/exemplo
```

Estas pastas seguem a convenção padrão do Maven para organização de código-fonte e recursos de teste.

2. Após criar todos os arquivos mencionados neste guia, sua estrutura de pastas deve se parecer com isto:

   cucumber-poc/
   ├── pom.xml
   ├── src
   │ ├── main
   │ │ └── java
   │ │ └── com
   │ │ └── exemplo
   │ │ ├── Usuario.java
   │ │ └── TestContext.java
   │ └── test
   │ ├── java
   │ │ └── com
   │ │ └── exemplo
   │ │ ├── RunCucumberTest.java
   │ │ └── StepDefinitions.java
   │ └── resources
   │ └── com
   │ └── exemplo
   │ └── gerenciar_usuario.feature
   └── target/

   Esta estrutura de pastas segue as melhores práticas para projetos Maven e Cucumber, separando claramente o código de produção, o código de teste e os recursos de teste.

## Passo 3: Criação das Classes Java

Agora, vamos criar as classes Java necessárias para nosso projeto.

1. Classe `Usuario`:

   Crie um novo arquivo chamado `Usuario.java` no diretório `src/main/java/com/exemplo/` com o seguinte conteúdo:

```java
package com.exemplo;

public class Usuario {
private String nome;
private int idade;

    public Usuario(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }

    public int getIdade() {
        return idade;
    }

    public void setIdade(int idade) {
        this.idade = idade;
    }

}
```

    Esta classe representa um usuário simples com nome e idade.

2. Classe `TestContext`:

   Crie um novo arquivo chamado `TestContext.java` no mesmo diretório com o seguinte conteúdo:

```java
package com.exemplo;

import java.util.HashMap;
import java.util.Map;

public class TestContext {
private Map<String, Object> contexto = new HashMap<>();

    public void setContexto(String chave, Object valor) {
        contexto.put(chave, valor);
    }

    public Object getContexto(String chave) {
        return contexto.get(chave);
    }

    public void limpar() {
        contexto.clear();
    }

}
```

    Esta classe gerencia o contexto compartilhado entre os steps do Cucumber.

3. Classe `RunCucumberTest`:

   Crie um novo arquivo chamado `RunCucumberTest.java` no diretório `src/test/java/com/exemplo/` com o seguinte conteúdo:

```java
package com.exemplo;

import io.cucumber.junit.Cucumber;
import io.cucumber.junit.CucumberOptions;
import org.junit.runner.RunWith;

@RunWith(Cucumber.class)
@CucumberOptions(
features = "src/test/resources/com/exemplo",
glue = "com.exemplo",
plugin = {"pretty", "html:target/cucumber-reports"}
)
public class RunCucumberTest {
}
```

    Esta classe é responsável por executar os testes Cucumber.

4. Classe `StepDefinitions`:

   Crie um novo arquivo chamado `StepDefinitions.java` no mesmo diretório com o seguinte conteúdo:

```java
package com.exemplo;

import io.cucumber.java.Before;
import io.cucumber.java.en.Given;
import io.cucumber.java.en.When;
import io.cucumber.java.en.Then;
import org.junit.Assert;

public class StepDefinitions {
private TestContext testContext = new TestContext();

    @Before
    public void setUp() {
        testContext.limpar();
    }

    @Given("eu tenho um usuário com nome {string}")
    public void euTenhoUmUsuarioComNome(String nome) {
        Usuario usuario = new Usuario(nome);
        testContext.setContexto("usuario", usuario);
    }

    @When("eu atualizo a idade do usuário para {int}")
    public void euAtualizoAIdadeDoUsuarioPara(Integer idade) {
        Usuario usuario = (Usuario) testContext.getContexto("usuario");
        usuario.setIdade(idade);
    }

    @Then("a idade do usuário deve ser {int}")
    public void aIdadeDoUsuarioDeveSer(Integer idadeEsperada) {
        Usuario usuario = (Usuario) testContext.getContexto("usuario");
        Assert.assertEquals(idadeEsperada, (Integer) usuario.getIdade());
    }

    @Then("o nome do usuário deve ser {string}")
    public void oNomeDoUsuarioDeveSer(String nomeEsperado) {
        Usuario usuario = (Usuario) testContext.getContexto("usuario");
        Assert.assertEquals(nomeEsperado, usuario.getNome());
    }

}
```

    Esta classe contém as definições dos steps do Cucumber.

## Passo 4: Criação do Arquivo Feature

Agora, vamos criar o arquivo feature do Cucumber que descreve nossos cenários de teste.

1. Crie um novo arquivo chamado `gerenciar_usuario.feature` no diretório `src/test/resources/com/exemplo/` com o seguinte conteúdo:

```gherkin
Feature: Gerenciar informações do usuário

Scenario: Criar um usuário e atualizar sua idade
Given eu tenho um usuário com nome "João"
When eu atualizo a idade do usuário para 30
Then a idade do usuário deve ser 30
And o nome do usuário deve ser "João"

Scenario: Criar outro usuário e verificar suas informações
Given eu tenho um usuário com nome "Maria"
When eu atualizo a idade do usuário para 25
Then a idade do usuário deve ser 25
And o nome do usuário deve ser "Maria"
```

Este arquivo descreve dois cenários de teste em linguagem Gherkin.

## Passo 5: Executando os Testes

Agora que temos toda a estrutura montada, vamos executar nossos testes.

1. Abra um terminal e navegue até o diretório raiz do projeto.
2. Execute o seguinte comando para compilar o projeto:

```bash
mvn clean compile
```

3. Em seguida, execute os testes com o comando:

```bash
mvn test
```

4. Após a execução, você verá o resultado dos testes no console. Além disso, um relatório HTML será gerado em `target/cucumber-reports/index.html`.

## Explicação Detalhada

- A classe `TestContext` age como um repositório central para armazenar e recuperar dados entre os steps do Cucumber.
- No método `setUp` da classe `StepDefinitions`, limpamos o contexto antes de cada cenário.
- Nos métodos anotados com `@Given`, `@When` e `@Then`, usamos o `TestContext` para armazenar e recuperar o objeto `Usuario`.
- O arquivo feature descreve os cenários de teste em uma linguagem natural, que é então mapeada para os métodos em `StepDefinitions`.

## Boas Práticas ao Usar Contexto Compartilhado

Ao implementar o contexto compartilhado em seus testes Cucumber, considere as seguintes boas práticas:

1. **Mantenha o contexto enxuto** : Armazene apenas as informações essenciais no contexto compartilhado. Excesso de dados pode tornar os testes difíceis de entender e manter.
2. **Limpe o contexto entre cenários** : Como demonstrado no método `setUp`, sempre limpe o contexto antes de cada cenário para evitar interferências entre testes.
3. **Use tipos fortemente tipados** : Quando possível, use classes específicas para armazenar dados no contexto, em vez de tipos primitivos ou strings genéricas.
4. **Documente o uso do contexto** : Mantenha uma documentação clara sobre quais dados são armazenados no contexto e como eles devem ser usados.
5. **Evite dependências complexas** : Se você perceber que está criando dependências complexas entre steps usando o contexto, considere refatorar seus cenários.

## Exemplos de Cenários Mais Complexos

Vamos expandir nosso exemplo para demonstrar um caso de uso mais complexo:

```gherkin
Feature: Gerenciar carrinho de compras

  Scenario: Adicionar itens ao carrinho e calcular o total
    Given eu tenho um usuário com nome "Alice"
    And eu crio um novo carrinho de compras
    When eu adiciono o produto "Livro" com preço 50.00 ao carrinho
    And eu adiciono o produto "Caneta" com preço 5.00 ao carrinho
    Then o carrinho deve conter 2 itens
    And o valor total do carrinho deve ser 55.00

  Scenario: Aplicar desconto ao carrinho
    Given eu tenho um usuário com nome "Bob"
    And eu crio um novo carrinho de compras
    And eu adiciono o produto "Notebook" com preço 1000.00 ao carrinho
    When eu aplico um desconto de 10%
    Then o valor total do carrinho deve ser 900.00
```

Para implementar estes cenários, você precisaria expandir sua classe `TestContext` e `StepDefinitions` para incluir lógica de carrinho de compras e cálculos de desconto.

## Desafios e Armadilhas Comuns

Ao implementar contexto compartilhado, esteja ciente dos seguintes desafios:

1. **Vazamento de estado** : Se o contexto não for limpo adequadamente entre os cenários, o estado de um teste pode afetar outro.
2. **Complexidade crescente** : À medida que mais dados são adicionados ao contexto, os testes podem se tornar difíceis de entender e manter.
3. **Acoplamento excessivo** : O uso excessivo do contexto compartilhado pode levar a um acoplamento forte entre os steps, dificultando a reutilização.
4. **Dificuldade de depuração** : Quando muitos dados são compartilhados, pode ser difícil rastrear a origem de um problema.
5. **Concorrência em execuções paralelas** : Se os testes são executados em paralelo, certifique-se de que o contexto seja thread-safe.

## Dicas para Depuração Efetiva

Ao depurar testes Cucumber com contexto compartilhado:

1. Use logs detalhados em seus steps para rastrear o estado do contexto.
2. Implemente um método para imprimir o conteúdo completo do contexto quando necessário.
3. Utilize breakpoints estratégicos em sua IDE para inspecionar o estado do contexto durante a execução.
4. Considere implementar um listener Cucumber personalizado para monitorar mudanças no contexto entre steps.

Exemplo de método para imprimir o contexto:

```java
public class TestContext {
    // ... outros métodos ...

    public void imprimirContexto() {
        System.out.println("Conteúdo do Contexto:");
        for (Map.Entry<String, Object> entry : contexto.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

## Integração com Desenvolvimento Orientado a Comportamento (BDD)

O uso de contexto compartilhado em testes Cucumber se alinha bem com os princípios do BDD:

1. **Foco no comportamento** : O contexto compartilhado permite que você mantenha o foco nos comportamentos descritos nos cenários, em vez de se preocupar com detalhes de implementação.
2. **Linguagem ubíqua** : Use o contexto para armazenar entidades e conceitos que fazem parte da linguagem ubíqua do seu domínio.
3. **Colaboração** : A estrutura clara proporcionada pelo contexto compartilhado facilita a colaboração entre desenvolvedores, testadores e stakeholders do negócio.
4. **Cenários vivos** : O contexto compartilhado ajuda a manter seus cenários "vivos" e executáveis, servindo como documentação ativa do comportamento do sistema.

Ao integrar esta abordagem com BDD, certifique-se de que seus cenários e steps permaneçam legíveis e alinhados com as expectativas do negócio, usando o contexto compartilhado para suportar, e não obscurecer, a narrativa dos testes.

## Conclusão

Neste guia detalhado, exploramos como implementar um contexto compartilhado em testes Cucumber com Java. Esta abordagem oferece várias vantagens:

1. Encapsulamento do estado compartilhado.
2. Facilidade de manutenção e expansão dos testes.
3. Clareza na separação entre a descrição dos cenários e sua implementação.
4. Organização estruturada do projeto, facilitando a navegação e manutenção do código.

Ao seguir estes passos, você criou uma base sólida para testes de aceitação robustos e fáceis de manter. Esta estrutura pode ser facilmente expandida para acomodar cenários de teste mais complexos em seus projetos reais.

A estrutura de pastas organizada que criamos garante uma separação clara entre o código de produção, os testes e os recursos, seguindo as convenções padrão do Maven e as melhores práticas para projetos Java. Isso não apenas melhora a legibilidade do projeto, mas também facilita a integração com ferramentas de build e CI/CD.

Ao implementar o contexto compartilhado em seus testes Cucumber, você não apenas melhora a organização e manutenção dos seus testes, mas também fortalece a prática de BDD em sua equipe. Lembre-se de que o objetivo final é criar testes que sejam claros, mantíveis e que efetivamente validem o comportamento do seu sistema. Use o contexto compartilhado como uma ferramenta para alcançar esse objetivo, sempre equilibrando a complexidade com a clareza e a eficácia dos seus testes.
