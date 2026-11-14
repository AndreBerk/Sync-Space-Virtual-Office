# SyncSpace - Virtual 2D Workspace & Collaboration Platform

> Uma plataforma web de colaboração em tempo real que simula um escritório físico em um ambiente 2D. O sistema utiliza áudio e vídeo por proximidade espacial, permitindo que usuários interajam, realizem reuniões e compartilhem telas de forma natural e orgânica, com arquitetura preparada para futura escalabilidade 3D.

---

## 1. Visão Geral e Escopo Estratégico

Ferramentas tradicionais de videoconferência geram fadiga e limitam a interação espontânea. O SyncSpace resolve esse problema gamificando o ambiente de trabalho remoto. O usuário controla um avatar em um mapa 2D; a comunicação de áudio e vídeo é estabelecida dinamicamente com base na distância entre os avatares, recriando a dinâmica de "encontrar alguém no corredor" ou "entrar em uma sala de reunião".

### Objetivos Primários:
1. Navegação Espacial 2D: Movimentação de avatares em tempo real sincronizada entre múltiplos clientes.
2. Áudio e Vídeo por Proximidade (Spatial Audio): Conexões WebRTC estabelecidas e ajustadas dinamicamente com base no raio de distância entre os usuários no mapa.
3. Colaboração Profissional: Zonas de interação específicas (ex: Salas de Reunião) que permitem compartilhamento de tela (Screen Sharing) e isolamento acústico do resto do mapa.
4. Escalabilidade Gráfica: Arquitetura desacoplada que permite a transição do motor gráfico 2D para renderização 3D (WebGL) em fases futuras do projeto.

---

## 2. Arquitetura de Software e Stack Tecnológico

O sistema exige altíssima performance para sincronização de frames e baixa latência de rede para streaming de mídia.

- Engine Gráfica (Frontend): HTML5 Canvas com Phaser.js (2D), operando em conjunto com React.js ou Next.js para a interface de usuário (HUD, chat, modais de login).
- Sincronização de Estado (Multiplayer Backend): Node.js com WebSockets (Socket.io ou Colyseus) para garantir a propagação da posição (X, Y) dos avatares a 60 FPS sem overhead de HTTP.
- Motor de Comunicação (Mídia): WebRTC (via LiveKit ou PeerJS) para transmissão peer-to-peer de vídeo, voz e captura de tela, minimizando o custo de banda do servidor.
- Banco de Dados: MongoDB ou PostgreSQL para persistência de dados de usuários, permissões de acesso e layouts de mapas.

---

## 3. Lógica de Domínio e Regras de Negócio

### 3.1. Engine de Proximidade (Spatial Connection)
- O servidor calcula constantemente a hipotenusa da distância entre as coordenadas (X, Y) dos clientes.
- Quando a distância atinge um limite configurado (ex: 50 pixels), o sistema negocia automaticamente as chaves SDP via WebRTC, conectando os fluxos de vídeo e áudio dos envolvidos.
- O volume do áudio diminui gradativamente conforme os avatares se distanciam (Fade Out espacial).

### 3.2. Zonas de Interação (Interactive Tiles)
O mapa 2D possui colisores invisíveis (Tilesets) que alteram o estado da aplicação:
- Zonas Privadas: Ao entrar em uma sala demarcada, o usuário só escuta e vê quem também está na sala, ignorando o raio de distância padrão.
- Telas de Apresentação: Objetos no mapa que, ao serem ativados, permitem que um usuário inicie o compartilhamento da tela do seu computador para todos na sala.

<img width="2752" height="1536" alt="1 (1)" src="https://github.com/user-attachments/assets/95a87c21-2491-4b0c-9380-f8a346484cc3" />
<img width="2752" height="1536" alt="1 (2)" src="https://github.com/user-attachments/assets/097a846b-899f-4f38-bf24-492713c26617" />


---

## 4. Levantamento de Requisitos

### Requisitos Funcionais (RF)
- RF01: O sistema deve permitir a movimentação fluida do avatar utilizando o teclado (W, A, S, D ou setas).
- RF02: O sistema deve solicitar e gerenciar permissões de câmera, microfone e compartilhamento de tela do navegador.
- RF03: O sistema deve conectar automaticamente os fluxos de mídia de usuários que entrarem no mesmo raio de aproximação.
- RF04: O sistema deve permitir o compartilhamento de tela inteira ou janela específica quando o usuário estiver em uma zona designada para apresentações.
- RF05: O sistema deve possuir um chat de texto global e um chat de texto privado (baseado na proximidade ou na sala).

### Requisitos Não Funcionais (RNF)
- RNF01: A latência máxima da comunicação de movimentação via WebSockets não deve ultrapassar 100ms.
- RNF02: O Frontend deve isolar a camada de renderização gráfica (Phaser.js) da camada de comunicação (WebRTC), facilitando o futuro upgrade para Three.js (3D).
- RNF03: A aplicação web deve ser otimizada para consumir o mínimo de memória RAM, gerenciando a destruição de instâncias de vídeo de usuários que saírem do campo de visão.
