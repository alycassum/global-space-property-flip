const properties = [
  { name: 'The Olive House', area: 'KARACHI · PECHS', description: 'Good bones, dated finishes, and a front garden that needs some love.', price: 8000000, baseResale: 11500000, baseBudget: 1500000, type: '3-bed · 1,450 sq ft', quote: 'The asking price is only half the story. Buy well, keep an eye on the renovation spend, and leave yourself room to make a profit.' },
  { name: 'The Courtyard Home', area: 'KARACHI · GULSHAN', description: 'A spacious family home with tired rooms and a courtyard worth showing off.', price: 11200000, baseResale: 15700000, baseBudget: 2100000, type: '4-bed · 1,900 sq ft', quote: 'Buyers pay for a home that feels considered. You do not need the most expensive finish in every room — you need the right finish.' },
  { name: 'The Clifton Residence', area: 'KARACHI · CLIFTON', description: 'A premium address with dated interiors. The margin is there if you plan the work carefully.', price: 18500000, baseResale: 25300000, baseBudget: 3200000, type: '3-bed · 2,200 sq ft', quote: 'At this price point, details matter. A coherent design story can be more valuable than a long list of expensive upgrades.' }
];
const upgrades = [
  { id: 'living', name: 'Living room', icon: '⌂', choices: [
    { id: 'budget', label: 'Refresh', cost: 250000, value: 420000, detail: 'Fresh paint, lighting and a tidy finish.' },
    { id: 'standard', label: 'Elevate', cost: 500000, value: 850000, detail: 'Better lighting, flooring and a cohesive palette.' },
    { id: 'premium', label: 'Signature', cost: 850000, value: 1300000, detail: 'Premium finishes and a designer-led look.' }
  ]},
  { id: 'kitchen', name: 'Kitchen', icon: '◇', choices: [
    { id: 'budget', label: 'Refresh', cost: 300000, value: 500000, detail: 'Repair cabinets, update handles and fittings.' },
    { id: 'standard', label: 'Elevate', cost: 650000, value: 1050000, detail: 'New worktop, fixtures and cabinet fronts.' },
    { id: 'premium', label: 'Signature', cost: 1100000, value: 1600000, detail: 'A complete, high-spec kitchen makeover.' }
  ]},
  { id: 'bathroom', name: 'Bathroom', icon: '≋', choices: [
    { id: 'budget', label: 'Refresh', cost: 200000, value: 350000, detail: 'New fittings, grout and a clean finish.' },
    { id: 'standard', label: 'Elevate', cost: 450000, value: 700000, detail: 'Updated vanity, tiles and modern fittings.' },
    { id: 'premium', label: 'Signature', cost: 750000, value: 1050000, detail: 'Premium surfaces and a hotel-inspired finish.' }
  ]}
];
const state = { cash: 10000000, flips: 0, propertyIndex: 0, phase: 'offer', purchase: 0, upgrades: {}, renovation: 0, sale: 0, lastProfit: 0, totalProfit: 0 };
const $ = (id) => document.getElementById(id);
const money = (value) => {
  const abs = Math.abs(value);
  if (abs >= 10000000) return `PKR ${(value / 10000000).toFixed(2)} Cr`;
  if (abs >= 100000) return `PKR ${(value / 100000).toFixed(abs >= 1000000 ? 1 : 0)} lakh`;
  return `PKR ${Math.round(value).toLocaleString('en-PK')}`;
};
const shortMoney = (value) => {
  if (Math.abs(value) >= 10000000) return `PKR ${(value / 10000000).toFixed(2)} Cr`;
  if (Math.abs(value) >= 100000) return `PKR ${(value / 100000).toFixed(1)} lakh`;
  return `PKR ${Math.round(value).toLocaleString('en-PK')}`;
};
function currentProperty() { return properties[state.propertyIndex]; }
function currentUpgradeCost() { return Object.values(state.upgrades).reduce((sum, choice) => sum + choice.cost, 0); }
function currentAddedValue() { return Object.values(state.upgrades).reduce((sum, choice) => sum + choice.value, 0); }
function projectedSale() { return currentProperty().baseResale + currentAddedValue() - Math.round(currentUpgradeCost() * 0.15); }
function projectedProfit() { return projectedSale() - state.purchase - currentUpgradeCost(); }
function toast(message) {
  const el = $('toast'); el.textContent = message; el.classList.add('show');
  clearTimeout(toast.timer); toast.timer = setTimeout(() => el.classList.remove('show'), 2800);
}
function updateCommon() {
  $('cashStat').textContent = money(state.cash);
  $('cashAvailable').textContent = money(state.cash);
  $('flipsStat').textContent = state.flips;
  $('ledgerPurchase').textContent = state.purchase ? money(state.purchase) : '—';
  $('ledgerRenovation').textContent = state.purchase ? money(currentUpgradeCost()) : '—';
  $('ledgerTotal').textContent = state.purchase ? money(state.purchase + currentUpgradeCost()) : '—';
  $('ledgerSale').textContent = state.sale ? money(state.sale) : '—';
  const profit = state.phase === 'results' ? state.lastProfit : (state.purchase ? projectedProfit() : 0);
  $('ledgerProfit').textContent = state.purchase ? money(profit) : '—';
  $('ledgerProfit').classList.toggle('negative', profit < 0);
}
function renderProperty() {
  const p = currentProperty();
  $('propertyName').textContent = p.name;
  $('propertyArea').textContent = p.area;
  $('propertyDescription').textContent = p.description;
  $('askingPrice').textContent = money(p.price);
  $('propertyType').textContent = p.type;
  $('alyQuote').textContent = `“${p.quote}”`;
  $('propertyArt').dataset.state = state.phase === 'results' ? 'after' : 'before';
  $('artLabel').textContent = state.phase === 'results' ? 'AFTER · READY TO SELL' : 'BEFORE · NEEDS WORK';
}
function renderOffer() {
  const p = currentProperty();
  $('stepEyebrow').textContent = 'YOUR FIRST MOVE';
  $('actionTitle').textContent = 'A promising project';
  $('stepCount').textContent = 'STEP 01 / 03';
  $('actionCopy').textContent = 'Aly has found a property with potential. Review the numbers before you commit.';
  $('dealMetrics').innerHTML = `<div><span>EST. RESALE VALUE</span><strong>${money(p.baseResale)}</strong></div><div><span>RENOVATION BUDGET</span><strong>${money(p.baseBudget)}</strong></div><div><span>PROJECTED PROFIT*</span><strong class="positive">${money(p.baseResale - p.price - p.baseBudget)}</strong></div>`;
  $('finePrint').textContent = '*Illustrative game figures before transaction costs. Your renovation choices will change the outcome.';
  $('actionFooter').innerHTML = `<span class="helper-text">Cash available: <strong>${money(state.cash)}</strong></span><button class="primary-button" id="primaryButton" type="button">Buy this property <span>→</span></button>`;
  $('primaryButton').addEventListener('click', buyProperty);
}
function buyProperty() {
  const p = currentProperty();
  if (state.cash < p.price) { toast('Not enough cash for this purchase. Try a smaller project.'); return; }
  state.cash -= p.price; state.purchase = p.price; state.upgrades = {}; state.renovation = 0; state.sale = 0; state.phase = 'renovate';
  $('alyQuote').textContent = '“Nice find. Now the margin depends on your choices. Each room has three finish levels — pick the ones that suit the buyer, not just your taste.”';
  render();
}
function renderRenovation() {
  $('stepEyebrow').textContent = 'MAKE YOUR MONEY WORK';
  $('actionTitle').textContent = 'Choose your upgrades';
  $('stepCount').textContent = 'STEP 02 / 03';
  $('actionCopy').textContent = 'Choose one finish level for each space. Premium looks impressive, but a smart refresh can protect your margin.';
  const grid = upgrades.map(room => {
    const selected = state.upgrades[room.id];
    return `<div class="reno-option-group"><div class="eyebrow" style="margin:0 0 7px 2px">${room.name.toUpperCase()}</div><div class="renovation-grid">${room.choices.map(choice => `<button type="button" class="reno-option ${selected?.id === choice.id ? 'selected' : ''}" data-room="${room.id}" data-choice="${choice.id}" aria-pressed="${selected?.id === choice.id}"><div class="reno-top"><span class="reno-icon">${room.icon}</span><span class="reno-check">${selected?.id === choice.id ? '✓ SELECTED' : '○'}</span></div><strong>${choice.label}</strong><span class="reno-cost">${shortMoney(choice.cost)}</span><small>${choice.detail}</small></button>`).join('')}</div></div>`;
  }).join('');
  $('dealMetrics').innerHTML = `<div><span>TOTAL INVESTED</span><strong>${money(state.purchase + currentUpgradeCost())}</strong></div><div><span>EST. SALE PRICE</span><strong>${money(projectedSale())}</strong></div><div><span>PROJECTED PROFIT</span><strong class="${projectedProfit() >= 0 ? 'positive' : 'negative'}">${money(projectedProfit())}</strong></div>`;
  $('finePrint').textContent = 'Upgrade value is simulated. Choose one option in each room; spending more does not always mean a better return.';
  $('actionFooter').innerHTML = `<span class="helper-text">Renovation spend: <strong>${money(currentUpgradeCost())}</strong></span><button class="primary-button" id="primaryButton" type="button" ${Object.keys(state.upgrades).length !== upgrades.length ? 'disabled' : ''}>Finish renovation <span>→</span></button>`;
  // Replace the three summary metrics with choices, while preserving the summary above them.
  $('dealMetrics').insertAdjacentHTML('afterend', `<div id="renovationChoices">${grid}</div><div class="reno-budget-line"><span>Purchase price</span><strong>${money(state.purchase)}</strong></div><div class="reno-budget-line"><span>Renovation total</span><strong id="renoTotal">${money(currentUpgradeCost())}</strong></div>`);
  document.querySelectorAll('.reno-option').forEach(button => button.addEventListener('click', () => chooseUpgrade(button.dataset.room, button.dataset.choice)));
  $('primaryButton').addEventListener('click', finishRenovation);
}
function chooseUpgrade(roomId, choiceId) {
  const room = upgrades.find(item => item.id === roomId);
  const choice = room.choices.find(item => item.id === choiceId);
  const previous = state.upgrades[roomId];
  const newTotal = currentUpgradeCost() - (previous ? previous.cost : 0) + choice.cost;
  if (newTotal > state.cash) { toast('Those upgrades exceed your available cash. Choose a more modest finish.'); return; }
  state.upgrades[roomId] = choice;
  render();
}
function finishRenovation() {
  if (Object.keys(state.upgrades).length !== upgrades.length) { toast('Choose a finish for all three spaces first.'); return; }
  const cost = currentUpgradeCost();
  if (state.cash < cost) { toast('You do not have enough cash for these upgrades. Choose a lower-cost finish.'); return; }
  state.cash -= cost;
  state.renovation = cost;
  state.sale = projectedSale();
  state.phase = 'sell';
  $('propertyArt').dataset.state = 'after';
  $('artLabel').textContent = 'AFTER · RENOVATION COMPLETE';
  $('alyQuote').textContent = '“That is a much stronger presentation. Now let’s see what the market is willing to pay — and whether your design choices paid off.”';
  render();
}
function renderSell() {
  $('stepEyebrow').textContent = 'TIME TO TEST THE MARKET';
  $('actionTitle').textContent = 'The property is ready';
  $('stepCount').textContent = 'STEP 03 / 03';
  $('actionCopy').textContent = 'The renovation is complete. Your sale offer reflects the property’s base value and the appeal added by your upgrades.';
  $('dealMetrics').innerHTML = `<div><span>TOTAL INVESTED</span><strong>${money(state.purchase + state.renovation)}</strong></div><div><span>SALE OFFER</span><strong>${money(state.sale)}</strong></div><div><span>EXPECTED PROFIT</span><strong class="${state.sale - state.purchase - state.renovation >= 0 ? 'positive' : 'negative'}">${money(state.sale - state.purchase - state.renovation)}</strong></div>`;
  $('finePrint').textContent = 'This simplified sale offer excludes taxes, agent fees, financing and other real-world transaction costs.';
  $('actionFooter').innerHTML = `<span class="helper-text">Ready to lock in your result?</span><button class="primary-button" id="primaryButton" type="button">Accept offer <span>→</span></button>`;
  $('primaryButton').addEventListener('click', sellProperty);
}
function sellProperty() {
  state.lastProfit = state.sale - state.purchase - state.renovation;
  state.cash += state.sale;
  state.totalProfit += state.lastProfit;
  state.flips += 1;
  state.phase = 'results';
  $('alyQuote').textContent = state.lastProfit >= 0 ? '“Well played. You bought with a plan, made considered upgrades, and left yourself a margin. Ready for a bigger challenge?”' : '“Every flip teaches you something. The next one is about buying more carefully and making every upgrade earn its place.”';
  render();
}
function renderResults() {
  const profit = state.lastProfit;
  const p = currentProperty();
  $('stepEyebrow').textContent = 'FLIP COMPLETE';
  $('actionTitle').textContent = profit >= 0 ? 'A deal well done.' : 'A lesson worth learning.';
  $('stepCount').textContent = 'RESULTS';
  $('actionCopy').textContent = profit >= 0 ? `You transformed ${p.name} and sold it. Your game profit is ${money(profit)} before real-world costs.` : `You sold ${p.name}, but the renovation spend outweighed the margin. Your game result is ${money(profit)}. Every flip is a chance to refine your strategy.`;
  $('dealMetrics').innerHTML = `<div><span>TOTAL INVESTED</span><strong>${money(state.purchase + state.renovation)}</strong></div><div><span>FINAL SALE</span><strong>${money(state.sale)}</strong></div><div><span>YOUR PROFIT</span><strong class="${profit >= 0 ? 'positive' : 'negative'}">${money(profit)}</strong></div>`;
  $('finePrint').textContent = `You have completed ${state.flips} flip${state.flips === 1 ? '' : 's'}. Cash on hand: ${money(state.cash)}. All values are game simulations.`;
  const hasNext = state.propertyIndex < properties.length - 1;
  $('actionFooter').innerHTML = `<span class="helper-text">Total profit across flips: <strong>${money(state.totalProfit)}</strong></span><button class="primary-button" id="primaryButton" type="button">${hasNext ? 'Find the next property' : 'Play another round'} <span>→</span></button>`;
  $('primaryButton').addEventListener('click', nextProperty);
}
function nextProperty() {
  if (state.propertyIndex < properties.length - 1) state.propertyIndex += 1;
  else state.propertyIndex = 0;
  state.phase = 'offer'; state.purchase = 0; state.upgrades = {}; state.renovation = 0; state.sale = 0; state.lastProfit = 0;
  render();
}
function render() {
  document.querySelectorAll('#renovationChoices, .reno-budget-line').forEach(el => el.remove());
  renderProperty();
  updateCommon();
  if (state.phase === 'offer') renderOffer();
  if (state.phase === 'renovate') renderRenovation();
  if (state.phase === 'sell') renderSell();
  if (state.phase === 'results') renderResults();
  updateCommon();
}
function restart() {
  state.cash = 10000000; state.flips = 0; state.propertyIndex = 0; state.phase = 'offer'; state.purchase = 0; state.upgrades = {}; state.renovation = 0; state.sale = 0; state.lastProfit = 0; state.totalProfit = 0;
  $('alyQuote').textContent = '“The asking price is only half the story. Buy well, keep an eye on the renovation spend, and leave yourself room to make a profit.”';
  render(); toast('Game restarted. Your starting cash is back.');
}
$('restartButton').addEventListener('click', restart);
render();
