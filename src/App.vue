<script setup>
</script>

<template>
  <div class="container">
    <h1>Owidiusz testuje CICD o 3:07</h1>
    <h1>Ela znalazła dobrą nutę</h1>

    <p>
      Visit
      <a href="https://vuejs.org/" target="_blank" rel="noopener">
        vuejs.org
      </a>
      to read the documentation
    </p>

    <h2>Lista zakupów – część 1</h2>
    <ul class="list">
      <li><input type="checkbox" /> Muszynianka 6 x 1.5l</li>
      <li><input type="checkbox" /> Muszynianka 6 x 1l</li>
      <li><input type="checkbox" /> Żywiec Zdrój 6 x 1.5l</li>
      <li><input type="checkbox" /> Kurczak 2 op.</li>
      <li><input type="checkbox" /> Mięso mielone 2 op.</li>
      <li><input type="checkbox" /> Łosoś puszka 4 op.</li>
      <li><input type="checkbox" /> Łosoś filet 1 op.</li>
      <li><input type="checkbox" /> Passata 2 szt.</li>
      <li><input type="checkbox" /> Mleko 6 x 1l</li>
      <li><input type="checkbox" /> Majonez 400ml</li>
      <li><input type="checkbox" /> Tortilla wieloziarnista 2 op.</li>
      <li><input type="checkbox" /> Ryż 4 x 100g</li>
      <li><input type="checkbox" /> Mango</li>
      <li><input type="checkbox" /> Ogórek 2 szt.</li>
      <li><input type="checkbox" /> Pomidory małe 2 op.</li>
      <li><input type="checkbox" /> Pomidory malinowe 4 szt.</li>
      <li><input type="checkbox" /> Serek Almette 2 op.</li>
      <li><input type="checkbox" /> Mozzarella kostka</li>
      <li><input type="checkbox" /> Ser żółty 1 op.</li>
      <li><input type="checkbox" /> Parówki 1 op.</li>
      <li><input type="checkbox" /> Chleby 5 szt.</li>
      <li><input type="checkbox" /> Dżem 1 op.</li>
      <li><input type="checkbox" /> Pizza mrożona 2 op.</li>
      <li><input type="checkbox" /> Nuggetsy 1 op.</li>
      <li><input type="checkbox" /> Ziemniaczki (Kaufland)</li>
      <li><input type="checkbox" /> Wędlina 3 rodzaje pakowanej</li>
      <li><input type="checkbox" /> Serek wiejski z owocami 6 op.</li>
      <li><input type="checkbox" /> Skyr 2 szt.</li>
      <li><input type="checkbox" /> Jogurt grecki 400ml</li>
      <li><input type="checkbox" /> Cebula</li>
      <li><input type="checkbox" /> Awokado</li>
      <li><input type="checkbox" /> Pomarańcze</li>
      <li><input type="checkbox" /> Cytryna</li>
      <li><input type="checkbox" /> Imbir</li>
      <li><input type="checkbox" /> Klinek 2 op.</li>
      <li><input type="checkbox" /> Pastella</li>
      <li><input type="checkbox" /> Roszponka / szpinak</li>
      <li><input type="checkbox" /> Małe mozzarelle</li>
    </ul>

    <h2>Lista zakupów – część 2</h2>
    <ul class="list">
      <li><input type="checkbox" /> Ciasto francuskie 3 op.</li>
      <li><input type="checkbox" /> Kabanosy 2 op.</li>
      <li><input type="checkbox" /> Mozzarella kostka</li>
      <li><input type="checkbox" /> Ketchup</li>
      <li><input type="checkbox" /> Tortilla 2 op.</li>
      <li><input type="checkbox" /> Almette 2 op.</li>
      <li><input type="checkbox" /> Łosoś kanapkowy 1 op.</li>
      <li><input type="checkbox" /> Ogórek 1 szt.</li>
      <li><input type="checkbox" /> Kuskus</li>
      <li><input type="checkbox" /> Mozzarella mała</li>
      <li><input type="checkbox" /> Mango</li>
      <li><input type="checkbox" /> Pomidorki</li>
      <li><input type="checkbox" /> Kurczak</li>
      <li><input type="checkbox" /> Roszponka</li>
      <li><input type="checkbox" /> Chrupki 4 op.</li>
      <li><input type="checkbox" /> Cola 3 szt.</li>
      <li><input type="checkbox" /> Sok</li>
      <li><input type="checkbox" /> Wino dla Żbiety</li>
    </ul>
  </div>
</template>

<style scoped>
.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1, h2 {
  margin-bottom: 10px;
}

.list {
  list-style: none;
  padding: 0;
}

.list li {
  margin: 6px 0;
  display: flex;
  align-items: center;
  gap: 8px;
}
</style>

