<div align="center">

# 🍕 Pizzaria Maranata — Cardápio Online

**Site de pedidos mobile-first que monta a pizza do cliente e envia o pedido pronto pelo WhatsApp.**
HTML, CSS e JavaScript puros · zero dependências em produção · sem build · sem cookies

[![CI](https://github.com/SEU-USUARIO/pizzaria-maranata/actions/workflows/ci.yml/badge.svg)](https://github.com/SEU-USUARIO/pizzaria-maranata/actions/workflows/ci.yml)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2020-f7df1e?logo=javascript&logoColor=black)
![Sem dependências](https://img.shields.io/badge/runtime-0%20depend%C3%AAncias-2ea44f)
![Licença](https://img.shields.io/badge/licen%C3%A7a-MIT-blue)

<img src="docs/screenshots/preview.png" alt="Telas do aplicativo: início, montagem da pizza, ingredientes e meu pedido" width="100%">

[**▶ Ver demonstração ao vivo**](https://SEU-USUARIO.github.io/pizzaria-maranata/) · [English summary](#english-summary)

</div>

---

## 💡 O problema

Pizzarias pequenas do interior vendem pelo WhatsApp: o cliente escreve o pedido à mão, o dono tenta entender sabor, tamanho, borda e endereço, e erros acontecem. Plataformas de delivery cobram comissão alta.

## ✅ A solução

Um cardápio web leve, pensado para celulares simples e conexões lentas, em que o cliente **monta o pedido clicando** e, no fim, o app abre o WhatsApp da pizzaria com **a mensagem já escrita** (itens, observações, total, entrega/retirada e forma de pagamento). Sem backend, sem mensalidade, sem comissão.

## ✨ Funcionalidades

| | |
|---|---|
| 🍕 **Montador de pizza** | 3 tamanhos, 1 ou 2 sabores (meio a meio com visual da pizza dividida), massa, borda recheada |
| 🧂 **Personalização por sabor** | Remover ingredientes da receita e adicionar extras com preço |
| 💰 **Preço calculado** | Sabor mais caro × fator do tamanho + borda + extras, tudo coberto por testes |
| 🛒 **Carrinho** | Quantidades, remoção, linhas iguais somadas, total em tempo real |
| 🔎 **Busca global** | Na tela inicial; ignora acentos (`agua` encontra `Água`) |
| 🥤 **Venda cruzada** | Após adicionar um item, o cliente cai em *Bebidas* com um aviso amigável |
| 📲 **Pedido via WhatsApp** | Mensagem formatada e corretamente codificada (`+`, `&`, acentos, emojis) |
| 🚦 **Regras da loja** | Pedido mínimo para entrega, status *Aberto/Fechado* pelo horário e fuso da loja |
| 📱 **Responsivo** | Feito para celular (Android/iOS, safe-area do iPhone); no desktop aparece enquadrado |
| ♿ **Acessibilidade** | Elementos clicáveis com teclado (`Tab`/`Enter`/`Esc`), `aria-label`, foco visível, `prefers-reduced-motion` |
| 🔒 **Privacidade (LGPD)** | Sem cookies, sem `localStorage`, sem analytics, sem fontes/CDN externos — o CI falha se algo externo aparecer |

## 🏗️ Arquitetura

Sem framework de propósito: o site precisa abrir em qualquer celular, hospedar de graça e ser fácil para um não-programador trocar preços.

```
index.html          estrutura das telas (sem JS inline)
css/style.css       tema (variáveis CSS), layout mobile-first, enquadramento no desktop
js/
  config.js         dados da loja: WhatsApp, horário, pedido mínimo, tempos
  data.js           cardápio inteiro (pizzas, porções, pastéis, sucos, bebidas)
  core.js           regras de negócio PURAS: preços, carrinho, mensagem, horário
  app.js            interface: renderização + eventos (delegação via data-act)
assets/img/         logo e fotos dos produtos
tests/
  unit/             regras de negócio e integridade do cardápio (Node, sem dependências)
  e2e/              fluxo do cliente no Chromium (Playwright)
scripts/            servidor local, verificações estáticas, gerador de screenshots
.github/workflows/  CI (testes) e deploy no GitHub Pages
```

Decisões de design que valem destacar:

- **Separação regra × interface.** `core.js` não toca no DOM e roda no navegador *e* no Node (padrão UMD simples), por isso é testado unitariamente sem navegador.
- **Dados fora do código.** Trocar um preço é editar uma linha em `data.js`; um teste valida que todo item tem preço, seção e foto existentes.
- **Delegação de eventos.** Nenhum `onclick` inline: elementos declaram `data-act="acao"` e um único listener despacha. Facilita CSP estrita e leitura.
- **Escape de HTML** em tudo que é renderizado via `innerHTML` (`Core.esc`).
- **Scripts clássicos (não ES modules)** para o `index.html` funcionar até aberto por duplo clique (`file://`), útil para o dono da pizzaria.
- **WhatsApp:** o link `wa.me` é aberto por um `<a>` criado e clicado, porque `window.open` é bloqueado por navegadores móveis fora de um gesto direto.

## 🚀 Rodando localmente

Não precisa instalar nada para **usar**: abra `index.html` no navegador. Para desenvolver:

```bash
git clone https://github.com/SEU-USUARIO/pizzaria-maranata.git
cd pizzaria-maranata
npm start                  # http://localhost:8080  (servidor estático, sem dependências)
npm run build:single       # gera dist/pizzaria-maranata.html (arquivo único para enviar por WhatsApp/e-mail)
```

## 🧪 Testes

```bash
npm install                # instala só o Playwright (dev)
npx playwright install chromium

npm run check              # sem recursos externos, sem handlers inline, arquivos existem
npm test                   # 21 testes unitários (preços, carrinho, mensagem, horário, cardápio)
npm run test:e2e           # 8 testes no Chromium: monta pizza → bebida → carrinho → WhatsApp
```

Os testes de ponta a ponta cobrem, entre outros: preço da pizza meio a meio com borda, regressão do bug de quantidade no carrinho, pedido mínimo de entrega, conteúdo exato da mensagem enviada ao `wa.me`, busca sem acento, personalização de ingredientes e o layout no desktop. O CI roda tudo a cada push.

## ⚙️ Personalização

| Quero mudar… | Edite |
|---|---|
| Número do WhatsApp, horário, pedido mínimo | `js/config.js` |
| Preços, sabores, produtos | `js/data.js` |
| Fotos | `assets/img/` (e o campo `foto` do item) |
| Cores e fontes | variáveis no topo de `css/style.css` |

> ⚠️ **Segurança:** por ser um site estático, o número do WhatsApp fica visível no código — o que é inerente a qualquer link `wa.me`. O que o cliente envia vai direto para o WhatsApp da loja; o site não coleta nem armazena dados.

## 🗺️ Próximos passos

- [ ] PWA (instalável, funciona offline para ver o cardápio)
- [ ] Campo de observações e endereço no pedido
- [ ] Painel simples para o dono editar o cardápio sem mexer em código
- [ ] Taxa de entrega por bairro

## 🖼️ Créditos e direitos de imagem

O código é licenciado sob **MIT** (veja [LICENSE](LICENSE)). O logotipo e as fotos dos produtos em `assets/img` são ilustrativos deste projeto de portfólio e pertencem aos seus respectivos titulares; substitua-os por imagens próprias antes de usar comercialmente.

---

## English summary

**Pizzaria Maranata** is a mobile-first online menu for a small pizzeria in Bom Jesus, Piauí (Brazil). Customers build a (half-and-half) pizza, customize ingredients, add drinks and sides, and the app opens WhatsApp with a ready-to-send order message — no backend, no framework, no cookies, zero runtime dependencies.

Highlights: pure business logic (`core.js`) shared by browser and Node and covered by 21 unit tests; 8 Playwright end-to-end tests covering the full order flow down to the exact `wa.me` URL; accent-insensitive search; delivery minimum and open/closed status by store timezone; keyboard-accessible UI; CI on GitHub Actions and deploy to GitHub Pages.

```bash
npm start      # serve on http://localhost:8080
npm test       # unit tests
npm run test:e2e
```
