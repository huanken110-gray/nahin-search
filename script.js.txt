// Simple client-side search data & logic
// Edit this `DATA` array to add/remove items.
const DATA = [
  {
    id: 1,
    title: "Facebook",
    url: "https://www.facebook.com",
    description: "Social networking site. Connect with friends & family.",
    category: "social",
    tags: ["social", "chat", "friends"]
  },
  {
    id: 2,
    title: "YouTube",
    url: "https://www.youtube.com",
    description: "Watch and share videos — movies, tutorials, music and more.",
    category: "other",
    tags: ["video", "movie", "music"]
  },
  {
    id: 3,
    title: "IMDB - Movies",
    url: "https://www.imdb.com",
    description: "Movie database: films, ratings, reviews, cast info.",
    category: "movie",
    tags: ["movie", "reviews", "cast"]
  },
  {
    id: 4,
    title: "Play Store",
    url: "https://play.google.com/store",
    description: "Download Android apps and games.",
    category: "app",
    tags: ["app", "android", "download"]
  }
];

// --- helpers ---
const el = id => document.getElementById(id);

function escapeHtml(str){
  return String(str).replace(/[&<>"']/g, s => ({
    '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'
  })[s]);
}

// highlight matched term (simple)
function highlight(text, term){
  if(!term) return escapeHtml(text);
  const t = term.replace(/[.*+?^${}()|[\]\\]/g,'\\$&');
  const re = new RegExp(t, 'ig');
  return escapeHtml(text).replace(re, m => `<span class="highlight">${m}</span>`);
}

// --- render results ---
function renderResults(list, query){
  const container = el('results');
  const info = el('resultsInfo');
  container.innerHTML = '';
  if(list.length === 0){
    info.textContent = `No results found for "${query}"`;
    return;
  }
  info.textContent = `${list.length} result(s) for "${query}"`;
  for(const item of list){
    const li = document.createElement('li');
    li.className = 'result';
    const title = `<a class="title" href="${escapeHtml(item.url)}" target="_blank" rel="noopener">${highlight(item.title, query)}</a>`;
    const desc = `<p class="desc">${highlight(item.description, query)}</p>`;
    const tags = `<div class="tags">${item.tags.map(t => `<span class="tag">${escapeHtml(t)}</span>`).join('')}</div>`;
    li.innerHTML = `${title}${desc}${tags}`;
    container.appendChild(li);
  }
}

// --- search logic ---
function search(query, category=''){
  if(!query && !category) return DATA.slice(0, 20); // show some default
  const q = (query || '').trim().toLowerCase();
  return DATA.filter(item => {
    if(category && item.category !== category) return false;
    if(!q) return true;
    // search in title, description, tags
    if(item.title.toLowerCase().includes(q)) return true;
    if(item.description.toLowerCase().includes(q)) return true;
    if(item.tags.join(' ').toLowerCase().includes(q)) return true;
    return false;
  });
}

// --- UI bindings ---
document.addEventListener('DOMContentLoaded', () => {
  const input = el('searchInput');
  const cat = el('categoryFilter');

  function doSearch(){
    const q = input.value;
    const c = cat.value;
    const results = search(q, c);
    renderResults(results, q);
  }

  input.addEventListener('input', doSearch);
  cat.addEventListener('change', doSearch);

  // initial
  renderResults(search('', ''), '');
});
