# ❤️ Nossa História - Pedido de Namoro

Um site interativo e romântico criado para celebrar momentos especiais e fazer um pedido inesquecível.

![Project Preview](assets/img/celebration.jpg)

## 📖 Sobre o Projeto

Este projeto é uma aplicação web desenvolvida como um gesto de amor. Ele apresenta uma linha do tempo de memórias, uma galeria de fotos interativa e culmina em um pedido de namoro especial com uma surpresa interativa.

O site foi construído com foco em design responsivo, animações suaves e uma experiência de usuário imersiva, utilizando navegação SPA (Single Page Application) para garantir que a música de fundo continue tocando enquanto o usuário navega entre as seções.

## ✨ Funcionalidades Principais

*   **🎵 Player de Música Persistente:** Um player de áudio personalizado que flutua na tela e continua tocando música ininterruptamente ao navegar entre as páginas (SPA).
*   **📸 Galeria Polaroid:** Fotos apresentadas em estilo polaroid, com legendas e animações ao passar o mouse. Ao clicar, um modal exibe a foto ampliada com detalhes.
*   **❤️ Interatividade no Pedido:** 
    *   Botão "Sim" que libera uma celebração com chuva de fogos (efeito visual) e uma mensagem especial.
    *   Botão "Não" que foge do cursor, tornando impossível recusar o pedido (brincadeira interativa).
*   **📱 Design Responsivo:** Layout totalmente adaptável para desktops, tablets e smartphones.
*   **🌗 Dark Mode:** Design elegante com tema escuro e detalhes em cores românticas (#ee2b5b).

## 🛠️ Tecnologias Utilizadas

*   **HTML5 Semântico**
*   **Tailwind CSS (via CDN):** Para estilização moderna e responsiva.
*   **JavaScript (Vanilla):**
    *   **History API & Fetch:** Para implementar a navegação SPA sem frameworks pesados.
    *   **DOM Manipulation:** Para o player de música, modais e efeitos interativos.
*   **Google Fonts:** Tipografia 'Be Vietnam Pro' e ícones 'Material Symbols'.

## 🚀 Como Executar Localmente

Devido ao uso de `fetch` para a navegação SPA e carregamento de arquivos, este projeto precisa ser executado através de um servidor HTTP local (o protocolo `file://` não suportará todas as funcionalidades).

### Opção 1: VS Code (Live Server)
1.  Instale a extensão **Live Server** no VS Code.
2.  Clique com o botão direito no arquivo `index.html`.
3.  Selecione "Open with Live Server".

### Opção 2: Python
Se você tem Python instalado:
```bash
# Navegue até a pasta do projeto
cd pasta-do-projeto

# Inicie um servidor simples
python -m http.server 8000
```
Acesse `http://localhost:8000` no seu navegador.

### Opção 3: Node.js (http-server)
```bash
npx http-server .
```

## 📂 Estrutura do Projeto

```
/
├── index.html          # Página principal (Home + Pedido)
├── memorias.html       # Página da galeria de fotos (carregada via SPA)
├── assets/
│   └── img/            # Imagens do projeto (polaroids, celebração)
├── musicas/            # Arquivos de áudio (.mp3)
└── README.md           # Documentação do projeto
```

## 🎨 Créditos

Desenvolvido com carinho por **Laercio Monteiro**.

---
*Feito com amor, para o meu amor.* ❤️
