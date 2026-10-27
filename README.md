<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="color-scheme" content="light dark">
<title>MarketPlace</title>
<style>
:root{
  --bg:#f3f6f8;--card:#fff;--ink:#14202b;--mute:#5d6b78;--line:#d9e1e7;
  --accent:#0b6e8a;--accent-ink:#fff;--tag:#ffd23f;--tag-ink:#14202b;
  box-sizing:border-box;
  padding-top:env(safe-area-inset-top,0px);
  padding-bottom:env(safe-area-inset-bottom,0px);
}
@media (prefers-color-scheme:dark){
  :root:not([data-theme="light"]){
    --bg:#0f171e;--card:#18232d;--ink:#eaf1f5;--mute:#93a3b0;--line:#2a3945;
    --accent:#4cc3e0;--accent-ink:#0f171e;
  }
}
*,*::before,*::after{box-sizing:inherit}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--ink);
  font:16px/1.45 -apple-system,BlinkMacSystemFont,"SF Pro Text","Segoe UI",Roboto,sans-serif;
  -webkit-text-size-adjust:100%}
h1,h2,h3{font-family:ui-rounded,"SF Pro Rounded",-apple-system,"Segoe UI",sans-serif;margin:0;letter-spacing:-.01em}
button,input,select{font:inherit;color:inherit}
button{cursor:pointer}
:focus-visible{outline:3px solid var(--accent);outline-offset:2px}
.wrap{max-width:1000px;margin:0 auto;padding:0 16px}
header{position:sticky;top:env(safe-area-inset-top,0px);z-index:5;background:var(--bg);border-bottom:1px solid var(--line)}
.bar{display:flex;align-items:center;gap:12px;padding:12px 0}
.logo{font-size:22px;font-weight:800;flex:1}
.logo span{background:var(--tag);color:var(--tag-ink);padding:0 8px;border-radius:8px;margin-left:2px}
.btn{border:0;border-radius:12px;padding:10px 14px;background:var(--accent);color:var(--accent-ink);font-weight:600}
.btn.ghost{background:transparent;color:var(--ink);border:1px solid var(--line)}
.search{width:100%;padding:12px 14px;border-radius:12px;border:1px solid var(--line);background:var(--card);margin-bottom:10px}
.chips{display:flex;gap:8px;overflow-x:auto;padding-bottom:12px;-webkit-overflow-scrolling:touch}
.chip{flex:none;border:1px solid var(--line);background:var(--card);border-radius:999px;padding:6px 14px}
.chip[aria-pressed="true"]{background:var(--ink);color:var(--bg);border-color:var(--ink)}
.hero{padding:24px 0 8px}
.hero h1{font-size:clamp(28px,6vw,44px);line-height:1.1;max-width:16ch}
.hero p{color:var(--mute);margin:8px 0 0;max-width:46ch}
.grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(200px,1fr));gap:14px;padding:16px 0 40px}
.item{background:var(--card);border:1px solid var(--line);border-radius:16px;overflow:hidden;display:flex;flex-direction:column}
.pic{font-size:64px;text-align:center;padding:22px 0;background:var(--bg)}
.info{padding:12px;display:flex;flex-direction:column;gap:4px;flex:1}
.info h3{font-size:16px}
.meta{color:var(--mute);font-size:13px}
.price{display:inline-block;align-self:flex-start;background:var(--tag);color:var(--tag-ink);font-weight:700;padding:2px 8px;border-radius:8px;margin-top:4px}
.row{display:flex;gap:8px;margin-top:auto;padding-top:10px}
.row .btn{flex:1;padding:8px}
.empty{grid-column:1/-1;text-align:center;color:var(--mute);padding:40px 0}
dialog{border:0;border-radius:18px;padding:0;background:var(--card);color:var(--ink);width:min(440px,calc(100% - 24px));max-height:85vh}
dialog::backdrop{background:rgba(0,0,0,.5)}
.dlg{padding:18px;display:flex;flex-direction:column;gap:10px}
.dlg label{display:flex;flex-direction:column;gap:4px;font-size:14px;color:var(--mute)}
.dlg input,.dlg select{padding:10px;border-radius:10px;border:1px solid var(--line);background:var(--bg);color:var(--ink)}
.line{display:flex;justify-content:space-between;align-items:center;gap:8px;padding:8px 0;border-bottom:1px solid var(--line)}
.total{font-weight:800;font-size:18px;display:flex;justify-content:space-between;padding-top:6px}
.qty button{width:30px;height:30px;border-radius:8px;border:1px solid var(--line);background:var(--bg)}
.qty span{display:inline-block;min-width:24px;text-align:center}
footer{color:var(--mute);font-size:13px;text-align:center;padding:20px 0 30px}
@media (prefers-reduced-motion:no-preference){.btn:active{transform:scale(.97)}}
</style>
</head>
<body>
<header>
  <div class="wrap">
    <div class="bar">
      <div class="logo">Market<span>Place</span></div>
      <button class="btn ghost" id="sellBtn">Продать</button>
      <button class="btn" id="cartBtn">Корзина · <b id="cnt">0</b></button>
    </div>
    <input class="search" id="q" type="search" placeholder="Что вы ищете?" aria-label="Поиск по товарам">
    <div class="chips" id="chips"></div>
  </div>
</header>

<main class="wrap">
  <section class="hero">
    <h1>Покупайте и продавайте рядом с собой</h1>
    <p>Выберите товар, добавьте в корзину и напишите продавцу в Telegram. Или выставите свой товар за минуту.</p>
  </section>
  <section class="grid" id="grid" aria-live="polite"></section>
</main>

<footer class="wrap">Демо-версия: товары и корзина хранятся только в вашем браузере.</footer>

<dialog id="cartDlg"><div class="dlg">
  <h2>Корзина</h2>
  <div id="cartBody"></div>
  <button class="btn" id="checkout">Написать продавцам</button>
  <button class="btn ghost" data-close>Закрыть</button>
</div></dialog>

<dialog id="sellDlg"><form class="dlg" id="sellForm" method="dialog">
  <h2>Новый товар</h2>
  <label>Название<input name="title" required maxlength="60"></label>
  <label>Цена, ₽<input name="price" type="number" min="1" required inputmode="numeric"></label>
  <label>Категория<select name="cat" id="catSel"></select></label>
  <label>Ваш Telegram (без @)<input name="tg" required maxlength="32" pattern="[A-Za-z0-9_]{4,32}"></label>
  <label>Эмодзи вместо фото<input name="emoji" maxlength="2" placeholder="📦"></label>
  <button class="btn" type="submit">Выставить товар</button>
  <button class="btn ghost" type="button" data-close>Отмена</button>
</form></dialog>

<script>
var CATS=["Электроника","Одежда","Дом","Хобби"];
var SEED=[
  {id:1,title:"Наушники беспроводные",price:3200,cat:"Электроника",tg:"seller_demo",emoji:"🎧"},
  {id:2,title:"Куртка демисезонная",price:4500,cat:"Одежда",tg:"seller_demo",emoji:"🧥"},
  {id:3,title:"Настольная лампа",price:1200,cat:"Дом",tg:"seller_demo",emoji:"💡"},
  {id:4,title:"Гитара акустическая",price:7800,cat:"Хобби",tg:"seller_demo",emoji:"🎸"},
  {id:5,title:"Кофеварка",price:2900,cat:"Дом",tg:"seller_demo",emoji:"☕"},
  {id:6,title:"Велосипед городской",price:12000,cat:"Хобби",tg:"seller_demo",emoji:"🚲"}
];
var items=SEED.slice(), cart={}, cat="Все";

function load(){
  try{
    var u=JSON.parse(localStorage.getItem("mp_items")||"[]");
    if(Array.isArray(u)) items=SEED.concat(u);
    var c=JSON.parse(localStorage.getItem("mp_cart")||"{}");
    if(c&&typeof c==="object") cart=c;
  }catch(e){}
}
function save(){
  try{
    localStorage.setItem("mp_items",JSON.stringify(items.filter(function(i){return i.id>1000})));
    localStorage.setItem("mp_cart",JSON.stringify(cart));
  }catch(e){}
}
function esc(s){return String(s).replace(/[&<>"']/g,function(c){return {"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]})}
function fmt(n){return Number(n).toLocaleString("ru-RU")+" ₽"}
function find(id){return items.filter(function(i){return i.id==id})[0]}

function renderChips(){
  var el=document.getElementById("chips"), all=["Все"].concat(CATS);
  el.innerHTML=all.map(function(c){
    return '<button class="chip" aria-pressed="'+(c===cat)+'" data-cat="'+esc(c)+'">'+esc(c)+'</button>';
  }).join("");
}
function renderGrid(){
  var q=document.getElementById("q").value.trim().toLowerCase();
  var list=items.filter(function(i){
    return (cat==="Все"||i.cat===cat)&&i.title.toLowerCase().indexOf(q)>-1;
  });
  document.getElementById("grid").innerHTML=list.length?list.map(function(i){
    return '<article class="item"><div class="pic" aria-hidden="true">'+esc(i.emoji||"📦")+'</div>'+
      '<div class="info"><h3>'+esc(i.title)+'</h3><div class="meta">'+esc(i.cat)+' · @'+esc(i.tg)+'</div>'+
      '<span class="price">'+fmt(i.price)+'</span>'+
      '<div class="row"><button class="btn" data-add="'+i.id+'">В корзину</button>'+
      '<a class="btn ghost" style="text-align:center;text-decoration:none" href="https://t.me/'+encodeURIComponent(i.tg)+'" target="_blank" rel="noopener">Написать</a></div></div></article>';
  }).join(""):'<p class="empty">Ничего не нашлось. Попробуйте другой запрос или категорию.</p>';
}
function renderCart(){
  var ids=Object.keys(cart).filter(function(id){return cart[id]>0&&find(id)});
  var n=0,sum=0;
  var html=ids.map(function(id){
    var i=find(id),q=cart[id];n+=q;sum+=i.price*q;
    return '<div class="line"><div>'+esc(i.title)+'<div class="meta">'+fmt(i.price)+'</div></div>'+
      '<div class="qty"><button data-dec="'+id+'" aria-label="Меньше">−</button><span>'+q+'</span><button data-inc="'+id+'" aria-label="Больше">+</button></div></div>';
  }).join("");
  document.getElementById("cnt").textContent=n;
  document.getElementById("cartBody").innerHTML=ids.length?html+'<div class="total"><span>Итого</span><span>'+fmt(sum)+'</span></div>':'<p class="meta">В корзине пусто. Добавьте товары из каталога.</p>';
  document.getElementById("checkout").hidden=!ids.length;
}
function change(id,d){cart[id]=Math.max(0,(cart[id]||0)+d);save();renderCart()}

document.addEventListener("click",function(e){
  var t=e.target.closest("[data-add],[data-inc],[data-dec],[data-cat],[data-close]");
  if(!t)return;
  if(t.dataset.add){change(t.dataset.add,1)}
  else if(t.dataset.inc){change(t.dataset.inc,1)}
  else if(t.dataset.dec){change(t.dataset.dec,-1)}
  else if(t.dataset.cat){cat=t.dataset.cat;renderChips();renderGrid()}
  else if(t.hasAttribute("data-close")){t.closest("dialog").close()}
});
document.getElementById("q").addEventListener("input",renderGrid);
document.getElementById("cartBtn").onclick=function(){renderCart();document.getElementById("cartDlg").showModal()};
document.getElementById("sellBtn").onclick=function(){document.getElementById("sellDlg").showModal()};
document.getElementById("checkout").onclick=function(){
  var tgs={};
  Object.keys(cart).forEach(function(id){var i=find(id);if(i&&cart[id]>0)tgs[i.tg]=1});
  var names=Object.keys(tgs);
  if(names.length)window.open("https://t.me/"+encodeURIComponent(names[0]),"_blank","noopener");
};
document.getElementById("sellForm").addEventListener("submit",function(e){
  var f=new FormData(e.target);
  items.push({id:Date.now(),title:f.get("title"),price:Number(f.get("price")),cat:f.get("cat"),tg:f.get("tg"),emoji:f.get("emoji")||"📦"});
  save();e.target.reset();renderGrid();
});

document.getElementById("catSel").innerHTML=CATS.map(function(c){return "<option>"+esc(c)+"</option>"}).join("");
load();renderChips();renderGrid();renderCart();
</script>
</body>
</html>

