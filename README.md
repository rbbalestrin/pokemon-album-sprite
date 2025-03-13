# Visualizador de Sprites Pokémon

Uma aplicação web para buscar, visualizar e exportar sprites de Pokémon com cores de fundo personalizáveis.

## Funcionalidades

- Buscar Pokémon por número (1-1025)
- Selecionar diferentes gerações e estilos de sprites
- Personalizar a cor de fundo
- Exportar o sprite com o fundo personalizado em uma imagem quadrada de 600x600 pixels

## Tecnologias Utilizadas

- [SvelteKit](https://kit.svelte.dev/) - Framework para criação de aplicações web
- [TailwindCSS](https://tailwindcss.com/) - Framework CSS para estilização
- [PokeAPI](https://pokeapi.co/) - API gratuita com dados de Pokémon

## Pré-requisitos

- [Node.js](https://nodejs.org/) (versão 18 ou superior)
- [npm](https://www.npmjs.com/) ou [Bun](https://bun.sh/)

## Instalação

1. Clone o repositório:

```bash
git clone https://github.com/seu-usuario/pokemon-sprites.git
cd pokemon-sprites
```

2. Instale as dependências:

```bash
# Com npm
npm install

# Com Bun
bun install
```

## Desenvolvimento

Para iniciar o servidor de desenvolvimento:

```bash
# Com npm
npm run dev

# Com Bun
bun run dev
```

A aplicação estará disponível em [http://localhost:5173](http://localhost:5173).

## Construção para Produção

Para construir o projeto para produção:

```bash
# Com npm
npm run build

# Com Bun
bun run build
```

Para visualizar a versão de produção localmente:

```bash
# Com npm
npm run preview

# Com Bun
bun run preview
```

## Como Usar

1. Digite o número do Pokémon que deseja visualizar (1-1025)
2. Clique em "Buscar" ou pressione Enter
3. Escolha a geração/estilo do sprite no menu suspenso
4. Selecione uma cor de fundo usando o seletor de cores
5. Clique em "Exportar Imagem" para baixar a imagem com o sprite e o fundo personalizado

## Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.

## Agradecimentos

- [PokeAPI](https://pokeapi.co/) por fornecer a API gratuita com dados de Pokémon
- [Svelte](https://svelte.dev/) pela excelente experiência de desenvolvimento
