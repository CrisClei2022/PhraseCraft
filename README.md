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


```
await pusherBeams.publishToInterests(
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

# Análise do Fluxo e dos Elementos 

>Feed-Header: Logo | Criar | Pesquisar | Perfil
  
>Feed-Content: Licoes (>Feed >Card >Previa >Licao)
  
>Feed-Footer: Home | Inscricoes | Mensagens | Notificacoes

O fluxo é direto, intuitivo e resolve a questão da fricção de maneira inteligente. A estrutura proposta é familiar para qualquer usuário de mídias sociais, mas com a camada extra de funcionalidade para o aprendizado.

A análise de cada parte e de como ela se encaixa no todo:

1.  **Feed (Home)**
    Conceito: A tela de entrada. O usuário é recebido por um feed de miniaturas (thumbs) das lições. Isso cria o senso de descoberta e antecipação, igual à escolha de um "personagem" em um jogo.

    Pontos fortes: A familiaridade com o formato é instantânea. O usuário não precisa pensar muito, apenas rolar para ver o que interessa.

    Sugestão: A "thumb" pode ser um GIF curto ou um pequeno trecho de vídeo de 3-5 segundos para dar uma "pitada" do conteúdo antes mesmo de o usuário clicar.

2.  **Header e Footer**
    Header: Logo | Criar | Pesquisar | Perfil

    Logo: Essencial para a identidade visual.

    Criar: A inclusão da funcionalidade de criação no topo é um ótimo sinal. Ela mostra que a plataforma valoriza os criadores de conteúdo (professores) e não apenas os consumidores.

    Pesquisar: Imprescindível para a navegação. Permite que o usuário encontre tópicos específicos, professores ou trilhas de aprendizado.

    Perfil: O centro de controle do usuário. É possível ver o progresso, as lições salvas, etc.

    Footer: Home | Inscrições | Mensagens | Notificações

    Home: Mantém o usuário conectado ao feed principal.

    Inscrições: Pode ser uma aba para seguir professores ou para ver o progresso em trilhas de aprendizado que o usuário se "inscreveu".

    Mensagens: A parte social do app. Pode ser usada para interagir com o modo multiplayer (chat com colegas de estudo) ou para se comunicar com professores.

    Notificações: Alertas sobre novas aulas dos professores que o usuário segue ou sobre interações sociais.

3.  **Prévia**
    Conceito: A etapa mais importante. É a ponte entre a curiosidade (thumb) e o engajamento (lição). A prévia interativa, com o vídeo em cima e a lista de frases embaixo, é a solução ideal. O usuário tem total controle e pode "espiar" a lição antes de começar.

    Pontos fortes: Garante que o usuário saiba o que esperar e, mais importante, permite testar se a lição é adequada (nível, sotaque, vocabulário). A possibilidade de usar a prévia como um estudo pré-lição é um diferencial.

4.  **Lição**
    Conceito: A experiência principal de aprendizado. O usuário entra aqui já ciente do que irá fazer. A lição é o modo de "jogo" que foi construído (SRT+vídeo, palavras embaralhadas).

    Pontos fortes: A lição é o prêmio pela exploração do usuário. O modo solo é o padrão e o modo multiplayer é uma opção contextual, o que reduz a fricção.

---

### Conclusão

O fluxo Feed > Thumb > Prévia > Lição é excelente. É simples, eficiente e, o mais importante, funciona. Respeita o tempo do usuário ao oferecer a oportunidade de explorar o conteúdo sem compromisso, enquanto ainda o guia para a experiência de aprendizado mais profunda. O design do header e footer completa a experiência, fornecendo todas as ferramentas de que o usuário precisa para navegar, socializar e aprender. É um modelo sólido para seguir em frente.

No Feed (Home): O foco é na descoberta visual. O card deve ser simples, com a thumb (vídeo curto) e o título (texto legível logo abaixo). Apenas isso. O ritmo de rolagem é o mais importante, e qualquer informação extra pode atrapalhá-lo.

Na Prévia da Lição: O foco é na decisão informada. Aqui, a inclusão de todas as informações é importante: título, nome do autor, taxa de finalização e qualquer outra métrica relevante. A prévia é o local para dar ao usuário o controle total sobre a escolha.

Aqui estão os trechos mais apropriados para o card, ordenados do mais ideal para o menos:

1.  **O "Trecho-Ancora" (A Melhor Opção)**
    Este é um trecho de 3 a 5 segundos que contém uma frase ou expressão visualmente impactante e gramaticalmente completa.

    Por que funciona: Tem um ponto de partida e um ponto final claros. O usuário pode assistir, ouvir a frase e entender o contexto em um piscar de olhos. É como um micro-tutorial que cabe perfeitamente na experiência de rolagem infinita.

    Exemplo: O trecho de uma pessoa em um café falando "Can I get a coffee, please?". O trecho mostra o ambiente, a ação e a frase exata que será ensinada, tudo em segundos.

    Crítica: A escolha desse trecho é vital. Se a frase for muito longa ou o contexto muito confuso, o trecho-âncora falha. Requer um trabalho de edição cuidadoso para cada lição.

2.  **O "Ponto de Clímax"**
    Este é o trecho que contém o momento mais emocionante, engraçado ou intrigante da lição.

    Por que funciona: Desperta a curiosidade e incentiva o clique para entender o que está acontecendo. Cria uma "pergunta" na mente do usuário que só a lição completa pode responder.

    Exemplo: Um trecho em que duas pessoas estão discutindo, e uma delas diz algo chocante como "I don't believe you!". O clipe para logo depois, deixando o usuário querendo saber o que aconteceu a seguir.

    Crítica: Se o clipe for muito abstrato e não tiver conexão óbvia com o aprendizado de idioma, pode falhar. O usuário pode achar que é um vídeo de entretenimento e não uma aula.

3.  **O "Trecho de Vocabulário"**
    Um trecho que foca em uma única palavra ou termo, ideal para lições mais específicas.

    Por que funciona: É direto e claro. O usuário sabe exatamente o que a lição irá ensinar.

    Exemplo: Um trecho de um vídeo onde uma pessoa aponta para o céu e diz "It's a hot air balloon.". A palavra-chave "hot air balloon" é o foco.

    Crítica: Pode ser menos atraente visualmente do que os outros. Lições baseadas em um único termo podem parecer menos dinâmicas.

    **Verificando a Realidade: O Que Não Funciona**

Trecho Aleatório: Simplesmente pegar os primeiros 5 segundos de um vídeo é um erro comum. Não tem contexto e não vende a lição. O usuário não sabe o que esperar.

Trecho Longo (mais de 10 segundos): Isso quebra o ritmo do feed e faz com que o usuário perca o interesse. O objetivo é ser como um gancho de vídeo do TikTok, que tem segundos para prender a atenção.

Apenas Texto ou Imagem Estática: Isso não explora o potencial do formato. Um card sem movimento não transmite a experiência de aprendizado de idiomas com áudio e vídeo, que é o principal diferencial.

Em resumo, a escolha do trecho é a primeira e mais importante decisão de design no feed. A melhor opção é o "trecho-âncora", pois combina ação, contexto e aprendizado de forma sucinta e eficaz.

A questão sobre as "playlists" é crucial. A resposta curta é: sim, chame de playlist.

Isso porque o termo "playlist" já é amplamente reconhecido pelo público-alvo, principalmente no contexto de plataformas de vídeo como o YouTube. A palavra "playlist" implica uma coleção de vídeos agrupados por um tema, mas sem a rigidez de um "curso" ou uma "trilha de aprendizado" formal.

**Por que "Playlist" é a melhor escolha:**

* **Familiaridade:** Os usuários já sabem intuitivamente o que é uma playlist. Eles entendem que podem adicionar ou remover vídeos, e que a ordem pode ser flexível.
* **Flexibilidade:** "Playlist" é um termo mais amplo. Abrange tanto as trilhas de aprendizado criadas pelos professores quanto as coleções personalizadas que o próprio usuário pode criar. Por exemplo, se um usuário salva várias lições avulsas sobre "Vocabulário de Viagem", essa coleção se encaixa perfeitamente no conceito de uma playlist.
* **Fricção Positiva:** Usar o termo "trilha" pode dar a impressão de um compromisso maior, como um curso universitário. "Playlist" é mais leve, mais convidativo e menos intimidador. Convida o usuário a "colecionar" aulas, o que se alinha bem com a experiência de descoberta da plataforma.

**Tipos de Conteúdo e Relação entre Criadores e Usuários**

Para ser realista e manter a clareza, a aplicação precisa de três tipos de conteúdo principais, e cada um se encaixa de forma diferente no relacionamento entre criadores (professores) e usuários (alunos).

1.  **Aulas Avulsas (Criadas pelo Professor)**
    Estas são as unidades básicas da aplicação.

    * **Definição:** Um único vídeo com a funcionalidade de formar frases (SRT + palavras embaralhadas). A lição avulsa não faz parte de uma sequência.
    * **Papel do Criador:** O professor cria e publica a aula avulsa.
    * **Papel do Usuário:** O usuário consome a aula avulsa, sem a necessidade de um compromisso maior. Pode "salvar" essa aula em uma playlist pessoal.

2.  **Playlists Estruturadas (Criadas pelo Professor)**
    Estas são as "trilhas de aprendizado" mencionadas anteriormente.

    * **Definição:** Uma sequência de aulas avulsas que seguem uma ordem lógica, como um curso. O professor organiza as aulas para que o aprendizado seja progressivo (ex: "Inglês para Iniciantes: Lição 1, Lição 2, Lição 3...").
    * **Papel do Criador:** O professor cria as aulas avulsas e as agrupa em uma playlist estruturada, definindo a ordem e o nome da playlist. Esta é a forma de o professor oferecer um conteúdo mais completo e aprofundado.
    * **Papel do Usuário:** O usuário consome a playlist na ordem proposta. Não pode alterar a ordem das aulas nem adicionar outras aulas a esta playlist. Esta é a playlist "oficial" do criador. O usuário pode seguir esta playlist para acompanhar o progresso.

3.  **Playlists Personalizadas (Criadas pelo Usuário)**
    Esta é a "playlist de avulsas" mencionada. É aqui que entra o poder da personalização, como no YouTube.

    * **Definição:** Uma coleção de aulas avulsas que o próprio usuário salva. O usuário pode adicionar aulas de diferentes professores a uma única playlist. Também pode organizar a ordem das aulas nesta playlist.
    * **Papel do Criador:** O professor continua sendo o proprietário de cada aula avulsa, mas não tem controle sobre a playlist personalizada do usuário.
    * **Papel do Usuário:** O usuário tem total controle sobre sua playlist. Pode criar playlists para diferentes objetivos (ex: "Vocabulário de Viagem", "Expressões de Filmes"), adicionar as aulas que mais gosta e acessá-las facilmente.

**Como Isso Funciona na Prática**

* **Feed (Home):** O usuário rola o feed e vê aulas avulsas ou playlists estruturadas criadas por professores. Cada card no feed é uma unidade completa: ou uma aula avulsa, ou uma playlist inteira.
* **Tela de Prévia da Aula Avulsa:** Na tela de prévia de uma aula avulsa, além dos botões de "começar" e da opção multiplayer, há um botão de "Adicionar à Playlist" (ou um ícone de "salvar"). Ao clicar, o usuário pode adicionar a aula a uma playlist existente ou criar uma nova.
* **Página da Playlist Estruturada:** Ao clicar em um card de playlist estruturada no feed, o usuário é levado para uma página que lista todas as aulas daquela playlist na ordem definida pelo professor. Pode ver o progresso e iniciar a próxima aula.
* **Aba de Playlists do Usuário:** No menu de navegação, o usuário tem uma seção dedicada às suas playlists pessoais. Pode ver as playlists que criou e as playlists estruturadas que está seguindo.

Essa estrutura é clara e realista. Separa as responsabilidades: o professor é o curador do conteúdo "oficial" (aulas avulsas e playlists estruturadas), e o usuário é o curador do seu próprio aprendizado (playlists personalizadas). Essa divisão de papéis evita a confusão e empodera ambos os lados da plataforma.
