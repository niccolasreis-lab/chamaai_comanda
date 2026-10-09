<script setup lang="ts">
import { computed, ref } from "vue";

type Product = { id: number; name: string; category: string; price: number };
type CartItem = Product & { quantity: number };

const categories = ["Todos", "Pizzas", "Bebidas", "Porcoes", "Adicionais"];
const products: Product[] = [
  { id: 1, name: "Calabresa da casa", category: "Pizzas", price: 52 },
  { id: 2, name: "Marguerita", category: "Pizzas", price: 48 },
  { id: 3, name: "Frango com catupiry", category: "Pizzas", price: 56 },
  { id: 4, name: "Coca-Cola 2 L", category: "Bebidas", price: 14 },
  { id: 5, name: "Suco de laranja", category: "Bebidas", price: 11 },
  { id: 6, name: "Batata rustica", category: "Porcoes", price: 29 },
  { id: 7, name: "Borda recheada", category: "Adicionais", price: 9 },
  { id: 8, name: "Molho especial", category: "Adicionais", price: 4 },
];

const activeCategory = ref("Todos");
const search = ref("");
const cart = ref<CartItem[]>([
  { ...products[0], quantity: 1 },
  { ...products[4], quantity: 2 },
]);
const orderType = ref("Mesa");
const selectedTable = ref("M04");

const filteredProducts = computed(() => products.filter((product) => {
  const categoryMatches = activeCategory.value === "Todos" || product.category === activeCategory.value;
  const searchMatches = product.name.toLowerCase().includes(search.value.toLowerCase());
  return categoryMatches && searchMatches;
}));
const subtotal = computed(() => cart.value.reduce((sum, item) => sum + item.price * item.quantity, 0));
const totalItems = computed(() => cart.value.reduce((sum, item) => sum + item.quantity, 0));

function addProduct(product: Product) {
  const existing = cart.value.find((item) => item.id === product.id);
  if (existing) existing.quantity += 1;
  else cart.value.push({ ...product, quantity: 1 });
}

function changeQuantity(id: number, delta: number) {
  const item = cart.value.find((entry) => entry.id === id);
  if (!item) return;
  item.quantity += delta;
  if (item.quantity <= 0) cart.value = cart.value.filter((entry) => entry.id !== id);
}

const currency = new Intl.NumberFormat("pt-BR", { style: "currency", currency: "BRL" });
</script>

<template>
  <main class="pos-shell">
    <header class="topbar">
      <div class="brand">
        <span class="brand-mark">CA</span>
        <div><strong>Chama Ai</strong><small>Comanda</small></div>
      </div>
      <div class="shift"><span class="live-dot"></span> Caixa aberto <strong>14:32</strong></div>
      <div class="operator"><span>NR</span><div><strong>Niccolas</strong><small>Operador</small></div></div>
    </header>

    <section class="workspace">
      <aside class="rail" aria-label="Navegacao principal">
        <button class="rail-item active" aria-label="Novo pedido"><span>+</span>Novo</button>
        <button class="rail-item" aria-label="Mesas"><span>12</span>Mesas</button>
        <button class="rail-item" aria-label="Pedidos"><span>8</span>Pedidos</button>
        <button class="rail-item" aria-label="Clientes"><span>48</span>Clientes</button>
        <div class="rail-spacer"></div>
        <button class="rail-item sync" aria-label="Sincronizacao"><span>OK</span>Online</button>
      </aside>

      <section class="catalog">
        <div class="catalog-heading">
          <div><h1>Novo pedido</h1><p>Selecione os itens para montar a comanda.</p></div>
          <label class="search"><span aria-hidden="true">⌕</span><input v-model="search" placeholder="Buscar produto" /></label>
        </div>
        <nav class="categories" aria-label="Categorias">
          <button v-for="category in categories" :key="category" :class="{ active: activeCategory === category }" @click="activeCategory = category">{{ category }}</button>
        </nav>
        <div class="product-grid">
          <button v-for="product in filteredProducts" :key="product.id" class="product" @click="addProduct(product)">
            <span class="product-thumb" :data-kind="product.category">{{ product.name.slice(0, 2).toUpperCase() }}</span>
            <span class="product-copy"><strong>{{ product.name }}</strong><small>{{ product.category }}</small></span>
            <b>{{ currency.format(product.price) }}</b>
          </button>
        </div>
      </section>

      <aside class="ticket">
        <div class="ticket-top">
          <div><span class="ticket-number">Pedido #0042</span><small>{{ totalItems }} itens</small></div>
          <button class="more" aria-label="Mais opcoes">•••</button>
        </div>
        <div class="service-switch">
          <button v-for="type in ['Mesa', 'Balcao', 'Delivery']" :key="type" :class="{ active: orderType === type }" @click="orderType = type">{{ type }}</button>
        </div>
        <label v-if="orderType === 'Mesa'" class="table-select">Mesa<select v-model="selectedTable"><option>M01</option><option>M02</option><option>M04</option><option>M05</option></select></label>

        <div class="items">
          <article v-for="item in cart" :key="item.id" class="ticket-item">
            <div><strong>{{ item.name }}</strong><small>{{ currency.format(item.price) }} cada</small></div>
            <div class="quantity"><button @click="changeQuantity(item.id, -1)">−</button><span>{{ item.quantity }}</span><button @click="changeQuantity(item.id, 1)">+</button></div>
            <b>{{ currency.format(item.price * item.quantity) }}</b>
          </article>
          <div v-if="cart.length === 0" class="empty">A comanda esta vazia.<br />Escolha um produto para comecar.</div>
        </div>

        <div class="summary">
          <div><span>Subtotal</span><strong>{{ currency.format(subtotal) }}</strong></div>
          <div><span>Taxa de servico</span><strong>{{ currency.format(0) }}</strong></div>
          <div class="total"><span>Total</span><strong>{{ currency.format(subtotal) }}</strong></div>
          <textarea placeholder="Observacao para a cozinha"></textarea>
          <div class="ticket-actions">
            <button class="hold">Colocar em espera</button>
            <button class="pay" :disabled="cart.length === 0">Receber {{ currency.format(subtotal) }}</button>
          </div>
        </div>
      </aside>
    </section>
  </main>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Archivo:wght@400;500;600;700;800&display=swap');
:root { font-family: Archivo, sans-serif; color: #251f1b; background: #e9e4db; font-synthesis: none; }
* { box-sizing: border-box; }
body { margin: 0; min-width: 1024px; min-height: 100vh; overflow: hidden; }
button, input, select, textarea { font: inherit; }
button { cursor: pointer; }
button:focus-visible, input:focus-visible, select:focus-visible, textarea:focus-visible { outline: 3px solid #f2a500; outline-offset: 2px; }
.pos-shell { min-height: 100vh; background: #f4f0e8; }
.topbar { height: 72px; padding: 0 22px; background: #261f1a; color: #fffaf1; display: flex; align-items: center; justify-content: space-between; }
.brand, .operator { display: flex; align-items: center; gap: 11px; }
.brand-mark { width: 40px; height: 40px; background: #f2a500; color: #261f1a; display: grid; place-items: center; border-radius: 12px; font-weight: 800; }
.brand div, .operator div { display: flex; flex-direction: column; line-height: 1.05; }
.brand strong { font-size: 18px; }.brand small { color: #f2a500; font-weight: 700; text-transform: uppercase; letter-spacing: .13em; font-size: 9px; margin-top: 5px; }
.shift { display: flex; align-items: center; gap: 9px; color: #d7cfc2; font-size: 13px; }.shift strong { color: #fff; margin-left: 8px; font-variant-numeric: tabular-nums; }
.live-dot { width: 8px; height: 8px; background: #62c977; border-radius: 50%; box-shadow: 0 0 0 4px #62c97722; }
.operator > span { width: 38px; height: 38px; background: #40362f; border-radius: 50%; display: grid; place-items: center; font-size: 12px; }.operator small { color: #b7ab9e; margin-top: 4px; font-size: 10px; }
.workspace { height: calc(100vh - 72px); display: grid; grid-template-columns: 82px minmax(540px, 1fr) 390px; }
.rail { background: #302822; padding: 18px 10px 12px; display: flex; flex-direction: column; gap: 8px; color: #d8cec2; }
.rail-item { border: 0; background: transparent; color: inherit; border-radius: 12px; padding: 9px 4px; display: flex; flex-direction: column; align-items: center; gap: 6px; font-size: 10px; font-weight: 600; }.rail-item span { font-size: 13px; font-variant-numeric: tabular-nums; }.rail-item.active { background: #f2a500; color: #261f1a; }.rail-spacer { flex: 1; }.rail-item.sync span { color: #62c977; }
.catalog { padding: 28px 30px; overflow-y: auto; }
.catalog-heading { display: flex; align-items: flex-end; justify-content: space-between; gap: 24px; }.catalog h1 { margin: 0; font-size: 30px; letter-spacing: -.03em; }.catalog p { margin: 7px 0 0; color: #766c63; font-size: 13px; }
.search { width: 270px; height: 42px; background: #fff; border-radius: 12px; display: flex; align-items: center; padding: 0 14px; gap: 8px; box-shadow: 0 5px 18px #382b1c12; }.search span { font-size: 22px; }.search input { border: 0; outline: 0; width: 100%; color: #251f1b; }
.categories { display: flex; gap: 8px; margin: 25px 0 22px; overflow-x: auto; }.categories button { border: 1px solid #ded6ca; background: transparent; color: #655c54; padding: 9px 15px; border-radius: 10px; font-size: 12px; font-weight: 600; }.categories button.active { border-color: #312820; background: #312820; color: #fff; }
.product-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(190px, 1fr)); gap: 14px; }.product { min-height: 204px; padding: 0; text-align: left; overflow: hidden; border: 0; border-radius: 15px; background: #fff; box-shadow: 0 7px 22px #3b2b1b10; display: grid; grid-template-columns: 1fr auto; grid-template-rows: 118px auto; transition: transform .16s ease-out, box-shadow .16s ease-out; }.product:hover { transform: translateY(-2px); box-shadow: 0 11px 25px #3b2b1b1c; }.product-thumb { grid-column: 1/-1; display: grid; place-items: center; color: #fff; background: #be4b31; font-size: 28px; font-weight: 800; letter-spacing: -.04em; }.product-thumb[data-kind="Bebidas"] { background: #355d75; }.product-thumb[data-kind="Porcoes"] { background: #ab6c2e; }.product-thumb[data-kind="Adicionais"] { background: #58714e; }.product-copy { padding: 15px 6px 14px 15px; display: flex; flex-direction: column; gap: 6px; }.product-copy strong { font-size: 13px; }.product-copy small { color: #8a7e72; font-size: 10px; }.product > b { padding: 15px 15px 0 0; font-size: 13px; color: #a4422c; font-variant-numeric: tabular-nums; }
.ticket { background: #fff; display: flex; flex-direction: column; box-shadow: -10px 0 28px #33261a12; min-width: 0; }.ticket-top { padding: 23px 22px 15px; display: flex; justify-content: space-between; align-items: center; }.ticket-top > div { display: flex; flex-direction: column; gap: 5px; }.ticket-number { font-weight: 800; font-size: 18px; }.ticket-top small { color: #887d72; }.more { border: 0; background: #f1ede7; width: 36px; height: 36px; border-radius: 10px; }
.service-switch { margin: 0 22px 14px; background: #f2eee8; padding: 4px; display: grid; grid-template-columns: repeat(3, 1fr); border-radius: 11px; }.service-switch button { border: 0; background: transparent; padding: 8px; border-radius: 8px; color: #786e65; font-size: 11px; font-weight: 700; }.service-switch button.active { background: #fff; color: #261f1a; box-shadow: 0 3px 10px #35291d16; }
.table-select { margin: 0 22px 14px; display: flex; align-items: center; justify-content: space-between; font-size: 12px; color: #6e645b; }.table-select select { border: 1px solid #ddd5ca; border-radius: 8px; padding: 6px 25px 6px 9px; background: #fff; font-weight: 700; }
.items { padding: 3px 22px; overflow-y: auto; flex: 1; border-top: 1px solid #eee9e2; }.ticket-item { display: grid; grid-template-columns: 1fr auto; gap: 11px; padding: 17px 0; border-bottom: 1px solid #eee9e2; }.ticket-item > div:first-child { display: flex; flex-direction: column; gap: 5px; }.ticket-item strong { font-size: 12px; }.ticket-item small { color: #8e8379; font-size: 10px; }.ticket-item > b { align-self: end; justify-self: end; font-size: 12px; font-variant-numeric: tabular-nums; }.quantity { display: flex; align-items: center; gap: 9px; }.quantity button { width: 25px; height: 25px; border: 1px solid #ddd4c9; border-radius: 7px; background: #fff; }.quantity span { width: 16px; text-align: center; font-size: 12px; font-variant-numeric: tabular-nums; }.empty { padding: 40px 10px; text-align: center; color: #8a8076; font-size: 12px; line-height: 1.6; }
.summary { padding: 18px 22px 22px; border-top: 1px solid #e9e3dc; }.summary > div:not(.ticket-actions) { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 12px; color: #766c63; }.summary .total { color: #251f1b; font-size: 18px; margin: 15px 0 16px; padding-top: 14px; border-top: 1px dashed #d9d0c5; }.summary textarea { width: 100%; height: 52px; resize: none; border: 1px solid #ded6cc; border-radius: 10px; padding: 10px; font-size: 11px; }.ticket-actions { display: grid; grid-template-columns: 1fr 1.45fr; gap: 9px; margin-top: 12px; }.ticket-actions button { min-height: 48px; border-radius: 11px; font-size: 11px; font-weight: 800; }.hold { background: #fff; border: 1px solid #d7cec3; color: #564b42; }.pay { border: 0; background: #f2a500; color: #261f1a; }.pay:disabled { opacity: .45; cursor: not-allowed; }
::selection { background: #f2a500; color: #261f1a; }::-webkit-scrollbar { width: 9px; height: 9px; }::-webkit-scrollbar-thumb { background: #c7bdb1; border: 2px solid transparent; background-clip: content-box; border-radius: 9px; }
@media (max-width: 1180px) { .workspace { grid-template-columns: 72px minmax(480px, 1fr) 340px; }.catalog { padding: 24px 20px; }.product-grid { grid-template-columns: repeat(auto-fill, minmax(170px, 1fr)); }.search { width: 220px; } }
</style>
