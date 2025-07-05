# PhraseCraft# 📝

---

## Sobre o Projeto

Este projeto é um aplicativo web interativo, desenvolvido com HTML, CSS e JavaScript puro, focado em ajudar os usuários a **montar frases embaralhadas**. A ideia central é proporcionar um jogo desafiador e divertido que, futuramente, se tornará uma ferramenta eficaz para o **estudo de idiomas**, com um foco inicial no **inglês**.

---

## Funcionalidades Atuais

* **Jogo de Frases Embaralhadas**: Os jogadores recebem palavras de uma frase em ordem aleatória e precisam organizá-las corretamente.
* **Modo de Dois Jogadores**: Permite que dois jogadores compitam para montar a frase mais rápido.
* **Sistema de Pontuação**: Pontos são atribuídos por acertos e oponentes ganham pontos em caso de erros ou tempo esgotado.
* **Cronômetro por Turno**: Cada jogador tem um tempo limitado para montar sua parte da frase, adicionando um elemento de pressão e agilidade.
* **Feedback Visual**: Indica claramente qual jogador está ativo e se a palavra clicada está correta ou errada.
* **Design Responsivo**: O layout se adapta a diferentes tamanhos de tela.

---

## Próximos Passos (Funcionalidades Futuras)

* **Estudo de Inglês**: Implementação de frases específicas para o aprendizado de vocabulário e gramática em inglês.
* **Áudios Nativos**: Capacidade de ouvir a pronúncia correta das frases, permitindo que os usuários pratiquem a escuta e a fala antes de montar a frase.
* **Progressão de Dificuldade**: Níveis de dificuldade variados para as frases.
* **Dicionário Integrado**: Possibilidade de consultar o significado das palavras.
* **Modo Single Player**: Opção para jogar individualmente e acompanhar o próprio progresso.

---

## Como Jogar (Versão Atual)

1.  Ao iniciar o jogo, uma frase embaralhada será exibida em cards de palavras.
2.  O **Jogador 1** começa. Clique nas palavras na ordem correta para montar a frase na área indicada.
3.  Um **cronômetro** indica o tempo restante para o turno do jogador atual.
4.  Se a palavra estiver correta, ela será adicionada à frase. Se estiver errada, o turno passa para o próximo jogador, e o oponente ganha um ponto.
5.  O turno passa para o **Jogador 2**.
6.  O jogo continua até que um jogador complete a frase corretamente.
7.  O jogador que completar a frase com o menor número de erros e mais rapidez (indiretamente, através da pontuação) vence a rodada.

---

## Tecnologias Utilizadas

* **HTML5**: Estrutura da página.
* **CSS3**: Estilização e responsividade.
* **JavaScript (ES6+)**: Lógica do jogo e interatividade.

---

## Contribuição

Contribuições são bem-vindas! Se você tiver sugestões ou quiser colaborar, sinta-se à vontade para abrir uma *issue* ou enviar um *pull request*.

---

## Instalação e Execução (Localmente)

1.  Clone o repositório:
    ```bash
    git clone [LINK_DO_SEU_REPOSITORIO]
    ```
2.  Navegue até o diretório do projeto:
    ```bash
    cd [NOME_DA_PASTA_DO_PROJETO]
    ```
3.  Abra o arquivo `index.html` em seu navegador de preferência.

---

**Desenvolvido com ❤️**

---

### Componentes Essenciais para Implementar Notificações Push

# Notificacao Enviada
* O interesse para o próximo jogador: atraves do interesse o Beams sabe para quem/onde deve enviar a notificacao.

# Notificacao Recebida
*  Quando toca na notificacao, a pagina carrega ou quando a pagina carrega ela verifica o body da ultima notificacao:
* A chave para isso é o Service Worker e a propriedade data da notificação push.

# Exemplo de Função Serverless (revisitado):


    ```await pusherBeams.publishToInterests(
  [`player-${nextPlayerId}`], // O interesse para o próximo jogador
  {
    web: {
      notification: {
        title: 'É a sua vez no jogo!',
        body: `Nova jogada: ${playString}`,
        // --- AQUI ESTÁ A CHAVE: Enviando dados para o Service Worker ---
        data: {
          gameId: 'ID_DA_PARTIDA_ATUAL', // ID da partida para saber qual jogo carregar
          nextTurnPlayerId: nextPlayerId, // ID do jogador que vai jogar (para sua lógica)
          lastPlayData: playString,       // A string da última jogada
          // ...outros dados que a página possa precisar (ex: estado do tabuleiro)
        },
        // --- Opcional: Para abrir uma URL específica ---
        deep_link: `https://seujogo.com/game?id=${'ID_DA_PARTIDA_ATUAL'}`, // URL da partida
      },
    },
  }
);
    ```

* **Pusher Beams (Instância e Configuração)**
    * **O que é:** O serviço principal para enviar notificações push aos navegadores dos jogadores.
    * **Sua parte:** Crie uma instância no painel do Pusher Beams e configure as **chaves VAPID** para Web Push. Anote seu **Instance ID** e sua **Secret Key**.

* **Appwrite Function (Função Serverless)**
    * **O que é:** Um pedaço de código que roda em um ambiente de servidor seguro, sem que você precise gerenciar o servidor.
    * **Sua parte:** Crie uma função no Appwrite para receber os dados da jogada do seu frontend. Dentro dela, use a **Secret Key do Pusher Beams** (armazenada de forma segura como variável de ambiente) para enviar a notificação ao jogador certo via Pusher Beams. Essa função é seu "backend invisível".

* **Service Worker (`sw.js`)**
    * **O que é:** Um script JavaScript que executa em segundo plano no navegador do jogador, independentemente de a sua página estar aberta.
    * **Sua parte:** Crie este arquivo. Ele vai interceptar as notificações push que chegam, exibi-las ao usuário e, quando o usuário clicar, abrir ou focar sua página, passando os dados da jogada para ela.

* **Seu Jogo (Frontend / Appwrite Pages)**
    * **O que é:** O código do seu jogo (HTML, CSS, JavaScript) hospedado no Appwrite Pages.
    * **Sua parte:**
        * **Inicialização:** Inclua o SDK do Pusher Beams e peça permissão ao usuário para notificações. Registre o dispositivo do jogador com um "interesse" (ex: `player-IDDoJogador`).
        * **Envio da Jogada:** Após uma jogada ser concluída, envie a string da jogada e o ID do próximo jogador para sua **Appwrite Function** (não diretamente para o Pusher Beams).
        * **Recebimento de Dados:** Implemente a lógica para que, ao carregar (seja via clique na notificação ou acesso direto), sua página possa ler os dados da jogada passados pelo Service Worker (via URL ou mensagens) e carregar o estado correto da partida.

* **Web App Manifest (`manifest.json`)**
    * **O que é:** Um arquivo JSON que define metadados sobre seu jogo, permitindo que ele seja "instalado" como um aplicativo na tela inicial do usuário (PWA).
    * **Sua parte:** Crie este arquivo com informações como nome, ícones e cores do seu jogo. Ele melhora a experiência da notificação push e faz seu jogo parecer mais com um app nativo.

---
