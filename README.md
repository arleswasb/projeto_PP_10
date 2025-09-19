# Projeto 08: Análise de Desempenho e Produtividade em OpenMP
### Comparativo de Mecanismos de Sincronização na Estimação de Pi

![Linguagem](https://img.shields.io/badge/Linguagem-C-blue.svg)
![Framework](https://img.shields.io/badge/Framework-OpenMP-orange.svg)
![Licença](https://img.shields.io/badge/Licença-MIT-green.svg)

## Descrição do Projeto

Este projeto, desenvolvido para a disciplina de Programação Paralela (DCA3703), realiza uma análise comparativa de diferentes estratégias de sincronização em OpenMP para resolver um problema clássico de acumulação paralela. O objetivo é avaliar o impacto no desempenho e na produtividade de cinco abordagens distintas para evitar condições de corrida na estimação do número Pi ($\pi$) pelo método de Monte Carlo.

Foram implementadas cinco versões para investigar o custo da contenção e a eficiência das abstrações do OpenMP:
1.  **Contador Compartilhado com Região Crítica (`critical`)**: Sincronização de baixa granularidade interna ao laço.
2.  **Contador Compartilhado com Operação Atômica (`atomic`)**: Alternativa de baixo nível com menor overhead, mas ainda com alta contenção.
3.  **Contador Privado com Sincronização Externa**: Padrão manual de alta granularidade para minimizar a contenção.
4.  **Cláusula `reduction`**: A abordagem idiomática e de mais alto nível do OpenMP para operações de redução.

## Conceitos Abordados

* Paralelismo de Dados com a diretiva `#pragma omp parallel for`.
* Sincronização para evitar Condições de Corrida.
* Método de Monte Carlo para estimação de $\pi$.
* Função reentrante `rand_r()` para geração de números aleatórios em paralelo.
* Comparativo entre **`#pragma omp critical`**, **`#pragma omp atomic`** e a cláusula **`reduction`**.
* Locking de Alta Granularidade (*Fine-Grained*) vs. Baixa Granularidade (*Coarse-Grained*).
* Análise de trade-offs entre Desempenho e Produtividade.

## Estrutura dos Arquivos

O repositório contém as implementações das diferentes estratégias de sincronização. Recomenda-se separar cada abordagem em um arquivo para clareza:

* `pi_critical_compartilhado.c`: Implementação com contador compartilhado e `#pragma omp critical` (Baixo Desempenho).
* `pi_atomic_compartilhado.c`: Implementação com contador compartilhado e `#pragma omp atomic` (Baixo Desempenho).
* `pi_privado_critical.c`: Implementação com contador privado e sincronização externa com `#pragma omp critical` (Alto Desempenho).
* `pi_reduction.c`: Implementação com a cláusula `reduction` (Melhor Desempenho e Produtividade).

## Como Compilar e Executar

O projeto foi desenvolvido em C e utiliza a biblioteca OpenMP. É necessário um compilador com suporte a OpenMP (como o GCC) e a flag `-fopenmp`.

### Compilação

```bash
# Exemplo para a versão com reduction (a mais eficiente)
gcc -o pi_reduction -fopenmp pi_reduction.c -lm

# Exemplo para a versão com critical em contador compartilhado (a menos eficiente)
gcc -o pi_critical_comp -fopenmp pi_critical_compartilhado.c -lm
```

### Execução

```bash
# Executar a versão com reduction
./pi_reduction

# Executar a versão com critical compartilhado para comparar o tempo
./pi_critical_comp
```

## Abordagens Implementadas

### Versões 1 e 2: Contador Compartilhado (Sincronização Interna)

Estas versões utilizam um único contador global. A proteção (`critical` ou `atomic`) ocorre **dentro** do laço principal, a cada vez que um ponto aleatório cai dentro do círculo. Esta abordagem de baixa granularidade cria um enorme gargalo de contenção, serializando as atualizações e anulando os ganhos do paralelismo.

### Versões 3 e 4: Contador Privado (Sincronização Externa)

Este padrão implementa uma estratégia de alta granularidade. Cada thread acumula o resultado em uma variável local privada, eliminando a contenção durante a execução do laço. A sincronização (`critical` ou `atomic`) ocorre apenas uma vez por thread, **após** o término do laço, para somar os subtotais ao contador global. O resultado é um desempenho drasticamente superior.

### Versão 5: Cláusula `reduction`

Esta versão utiliza a abstração de mais alto nível do OpenMP para este problema. A cláusula `reduction(+:contador)` instrui o compilador a aplicar o padrão de contador privado de forma automática e otimizada. É a abordagem que combina o melhor desempenho com o código mais limpo e produtivo.

## Análise e Conclusões

A análise das cinco implementações, detalhada no relatório e na apresentação, leva às seguintes conclusões:

* A **contenção de recursos** é o principal fator limitante de desempenho. A localização da sincronização (interna vs. externa ao laço) é mais crucial do que a diretiva específica (`atomic` vs. `critical`).
* A estratégia de **alta granularidade** (um contador por thread, seja manual ou via `reduction`) é fundamental para alcançar a escalabilidade em problemas de acumulação.
* A cláusula **`reduction`** é superior em todos os aspectos: oferece o melhor desempenho, resulta no código mais conciso e seguro, e aumenta a produtividade do programador.
* **Regra Geral:** Sempre prefira a abstração de mais alto nível que o OpenMP oferece para resolver o seu problema, pois ela geralmente encapsula as melhores práticas de desempenho.

## Autor

* **Werbert Arles de Souza Barradas**

-----

**Disciplina:** DCA3703 - Programação Paralela - T01 (2025.2)  
**Docente:** Professor Doutor Samuel Xavier de Souza  
**Instituição:** Universidade Federal do Rio Grande do Norte (UFRN)
