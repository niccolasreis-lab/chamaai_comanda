# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

Operadores de caixa, atendentes e gestores de pequenos restaurantes e pizzarias. O uso principal acontece em balcões de atendimento, com operação rápida por mouse, teclado ou tela sensível ao toque.

## Product Purpose

Chama Aí Comanda centraliza pedidos de balcão, mesa e delivery. O caixa deve continuar operando durante quedas de internet e sincronizar os pedidos quando a conexão retornar. O sucesso significa registrar e finalizar pedidos com rapidez, sem perder dados financeiros ou interromper o atendimento.

## Positioning

O produto combina um PDV desktop offline-first, integrado diretamente à impressão térmica, com uma API central para administração, delivery, iFood e WhatsApp.

## Operating Context

A operação inicial contempla de um a três caixas simultâneos. O PDV roda como aplicativo Tauri no Windows, mantém dados operacionais em SQLite e sincroniza com uma API Laravel. Impressoras ESC/POS genéricas serão suportadas inicialmente. O backend roda em contêineres Docker localmente e poderá ser publicado em uma VPS posteriormente.

## Capabilities and Constraints

- Pedidos de balcão, mesa, delivery e iFood.
- Operação offline com fila idempotente e identificadores ULID.
- Impressão térmica ESC/POS genérica.
- Integração futura com iFood, WhatsApp e PIX.
- Backend Laravel 12 em PHP 8.3, MySQL 8 e Redis.
- Desktop Tauri v2 com Vue 3 e TypeScript.
- O sistema legado CodeIgniter 2 permanece somente como referência de migração em `legacy/`.
- O deploy atual é Docker local; host remoto ainda não foi definido.

## Brand Commitments

O nome do produto é Chama Aí Comanda. A linguagem da interface deve ser direta, brasileira e adequada a uma operação de restaurante sob pressão.

## Evidence on Hand

- Sistema legado completo em `legacy/`.
- Banco histórico em `legacy/banco_de_dados.sql`.
- Imagens de produtos e sons operacionais em `legacy/assets/`.
- Não existem depoimentos, métricas comerciais ou credenciais de integrações; esses itens não devem ser fabricados.

## Product Principles

- O caixa nunca para por falta de internet.
- Valores e estados de pedidos são consistentes e auditáveis.
- As ações mais frequentes exigem poucos passos e têm retorno imediato.
- Integrações externas não bloqueiam a operação local.
- Segurança e manutenção moderna substituem os atalhos do sistema legado.

## Accessibility & Inclusion

A interface deve funcionar por teclado e toque, manter contraste AA e evitar depender apenas de cor para comunicar estados.
