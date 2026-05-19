# Documento de elicitação de requisitos — Mesma Onda

**Autor:** Carlos Leonardo
**Ano:** 2026

---

## 1. Sobre o projeto

Mesma Onda é uma aplicação web de perguntas diárias com mecânica social. Todo dia uma pergunta subjetiva é publicada e todos os usuários respondem com um valor numérico. A pontuação de cada usuário é calculada com base na proximidade da sua resposta em relação à mediana coletiva do dia, quanto mais próximo da mediana, maior a pontuação. O objetivo é acumular pontos ao longo dos dias pensando como a maioria pensa.

A aplicação contará com três rankings competitivos (diário, semanal e mensal). A pontuação de cada resposta é computada uma única vez e alimenta os três rankings simultaneamente. O ranking diário reseta à meia-noite, o semanal todo domingo e o mensal todo primeiro dia do mês, sempre no horário de Brasília. O objetivo do usuário é acumular a maior pontuação possível em cada janela de tempo.

### 1.1 Fórmulas

**Precisão da resposta (%)**

Mede quão perto a resposta do usuário ficou da mediana coletiva, normalizada de 0 a 100.

```
precisão = max(0, (1 - |resposta - mediana| / range_max) * 100)
```

Onde `range_max = valor_max - valor_min` da pergunta.

**Pontos da resposta**

Aplica uma curva quadrática (k=2) sobre a precisão para premiar respostas próximas da mediana.

```
pontos = round((precisão / 100)^2 * 100)
```

**Precisão média do usuário (incremental)**

Média aritmética das precisões obtidas ao longo do tempo, atualizada de forma incremental para garantir leitura O(1).

```
soma_precisões  ← soma_precisões + precisão_nova
total_respostas ← total_respostas + 1

precisão_média = soma_precisões / total_respostas
```

---

## 2. Requisitos funcionais

| ID | Nome | Descrição | Prioridade | Status |
|---|---|---|---|---|
| RF-01 | Login com Google | O sistema deve ser capaz de realizar login com a conta do Google do usuário. | Média | Aguardando |
| RF-02 | Cadastro com Google | O sistema deve ser capaz de cadastrar novos usuários utilizando suas contas do Google. | Média | Aguardando |
| RF-03 | Login com e-mail e senha | O sistema deve ser capaz de realizar o login do usuário utilizando email e senha. | Alta | Aguardando |
| RF-04 | Cadastro com e-mail e senha | O sistema deve ser capaz de cadastrar um usuário com email e senha. | Alta | Aguardando |
| RF-05 | Entrar como convidado | O sistema deve permitir que um visitante não autenticado responda à pergunta do dia. A sessão de convidado não tem persistência de dados e nem pode ver a mediana do dia. O sistema deve oferecer a opção de criar conta para salvar o resultado e ver o quão perto o usuário ficou da mediana. | Média | Aguardando |
| RF-06 | Troca de senha | O sistema deve ser capaz de realizar a troca da senha da conta de um usuário. | Alta | Aguardando |
| RF-07 | Cadastro da resposta do usuário | O sistema deve cadastrar a resposta de todos os usuários. | Alta | Aguardando |
| RF-08 | Cálculo da mediana | O sistema deve manter a mediana coletiva atualizada conforme novas respostas são processadas, utilizando cálculo incremental (sem varredura completa do conjunto de respostas a cada submissão). | Alta | Aguardando |
| RF-09 | O sistema deve possuir três rankings | O sistema deve possuir os rankings diário, semanal e mensal. | Média | Aguardando |
| RF-10 | O sistema deve recalcular os três rankings | O sistema deve ser capaz de recalcular os três rankings. | Média | Aguardando |
| RF-11 | Registro de nome de usuário | O sistema deve receber e cadastrar um nome de usuário para cada usuário. | Alta | Aguardando |
| RF-12 | Armazenamento de respostas antigas dos usuários | O sistema deve armazenar todas as respostas antigas dos usuários. | Baixa | Aguardando |
| RF-13 | Armazenamento de perguntas | O sistema deve armazenar todas as perguntas e os seus dados. | Média | Aguardando |
| RF-14 | Contagem de dias em sequência | O sistema deve contar há quantos dias o usuário está respondendo em sequência. | Baixa | Aguardando |
| RF-15 | Contagem de pontos totais | O sistema deve somar todos os pontos já feitos pelo usuário. | Baixa | Aguardando |
| RF-16 | Precisão média do usuário | O sistema deve calcular e exibir a precisão média do usuário conforme fórmula definida na seção 1.1, atualizada de forma incremental a cada nova resposta. | Média | Aguardando |
| RF-17 | Recorde de dias em sequência | O sistema deve guardar o recorde de dias em sequência do usuário. | Baixa | Aguardando |
| RF-18 | Contagem de jogadores diários | O sistema deve guardar quantos jogadores jogaram em determinado dia. | Média | Aguardando |
| RF-19 | Validação do range da resposta | O sistema deve rejeitar qualquer resposta cujo valor esteja fora do intervalo `[valor_min, valor_max]` declarado pela pergunta corrente, retornando erro ao cliente antes de persistir. | Alta | Aguardando |
| RF-20 | Exclusão de conta | O sistema deve permitir que o usuário exclua a sua conta. | Alta | Aguardando |
| RF-21 | Permitir Logout | O sistema deve permitir que o usuário realize logout de sua conta. | Alta | Aguardando |
| RF-22 | Exibição da pontuação | O sistema deve exibir a mediana da pergunta e a pontuação do jogador junto da sua resposta. | Alta | Aguardando |
| RF-23 | Exibição de respostas passadas | O sistema deve exibir na aba de perfil todas as respostas passadas do usuário. | Média | Aguardando |
| RF-24 | Ocultação de mediana | O sistema deve ocultar a mediana atual da pergunta do dia para usuários que ainda não responderam. | Alta | Aguardando |
| RF-25 | Reset de rankings | O sistema deve executar reset automático dos rankings: o ranking diário às 00:00 BRT de cada dia, o semanal às 00:00 BRT de todo domingo, e o mensal às 00:00 BRT do primeiro dia de cada mês. O ranking encerrado deve ser arquivado para consulta histórica antes do reset. | Alta | Aguardando |
| RF-26 | Pergunta do dia | O sistema deve publicar uma pergunta nova a cada dia às 00:00 BRT, garantindo que apenas uma pergunta esteja ativa por vez. | Alta | Aguardando |
| RF-27 | Unicidade da resposta | O sistema deve impedir que o mesmo usuário responda mais de uma vez à mesma pergunta. | Alta | Aguardando |
| RF-28 | Contagem de pontos | O sistema deve aceitar respostas apenas para a pergunta ativa do dia corrente, para contagem de pontos e participação no ranking. | Alta | Aguardando |
| RF-29 | Tipos de pergunta | O sistema deve suportar diferentes tipos de input para resposta, identificados por um campo `tipo_input` em cada pergunta. No MVP, apenas o tipo `numerico_linear` é implementado. A arquitetura deve permitir a adição futura de novos tipos (ex: cor, dia da semana, hora) sem alteração das tabelas de respostas e rankings, apenas via novos componentes de input e, quando necessário, novas funções de cálculo de distância. | Alta | Aguardando |
| RF-30 | Modo diversão | O usuário pode responder perguntas passadas, mas elas não terão peso para a sua pontuação, precisão ou participação no ranking. Servirão apenas para verificar o quão próximo o usuário chega da mediana histórica daquela pergunta. | Média | Aguardando |
| RF-31 | Respostas do modo convidado | Usuários convidados não têm a sua resposta registrada no sistema. | Alta | Aguardando |
| RF-32 | Metadados da pergunta | Cada pergunta deve declarar: enunciado textual, tipo de input, valor mínimo aceito, valor máximo aceito, e data de publicação. | Alta | Aguardando |