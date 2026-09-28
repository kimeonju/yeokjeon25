<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>역전식자재</title>

<style>
body{font-family:Arial;margin:0;background:#f5f5f5}
header{background:#198754;color:white;text-align:center;padding:20px}
nav{text-align:center;background:white;padding:12px}
nav a{margin:8px;color:#198754;text-decoration:none}
section{background:white;margin:15px auto;padding:15px;max-width:900px;border-radius:8px}
.box{display:flex;gap:15px;max-width:930px;margin:auto}
.box section{width:50%}
input{padding:9px;width:100%;box-sizing:border-box}
button{padding:9px;background:#198754;color:white;border:0;margin-top:5px}
.post,.product{padding:10px;border-bottom:1px solid #ddd}
#postList,#productList{max-height:300px;overflow-y:auto}
table{width:100%;border-collapse:collapse}
th,td{border:1px solid #ddd;padding:8px;text-align:center}
footer{text-align:center;padding:20px}
#map{width:100%;height:400px;margin-top:20px}
@media(max-width:600px){
.box{display:block}
.box section{width:auto}
}
</style>
</head>

<body>

<header>
<h1>역전식자재</h1>
<p>신선한 식자재, 믿을 수 있는 역전식자재</p>
</header>

<nav>
<a href="#posts">게시글</a>
<a href="#products">상품</a>
<a href="#donation">기부금</a>
<a href="#contact">문의</a>
</nav>

<div class="box">

<section id="posts">
<h2>게시글</h2>
<input id="postSearch" placeholder="게시글 검색">
<button onclick="searchPosts()">검색</button>
<div id="postList"></div>
</section>

<section id="products">
<h2>상품</h2>
<input id="productSearch" placeholder="상품 검색">
<button onclick="searchProducts()">검색</button>
<div id="productList"></div>
</section>

</div>

<section id="donation">
<h2>기부금 사용내역</h2>
<table>
<tr><th>날짜</th><th>내용</th><th>금액</th></tr>
<tr><td>2026-01-01</td><td>어촌계연합 기부</td><td>100,000원</td></tr>
<tr><td>2026-02-01</td><td>환경운동연합 기부</td><td>200,000원</td></tr>
</table>
<p><b>잔액 0원</b></p>
</section>

<section id="contact">
<h2>문의</h2>
<p>전화: <a href="tel:01026946608">010-2694-6608</a></p>
<p><a href="https://forms.gle/G9Bxju48dDDFhyMBA" target="_blank">문의하기</a></p>
</section>

<footer>
© 2026 역전식자재. All rights reserved.
<div id="map"></div>
</footer>

<script>
const posts=[
{title:"역전식자재 오픈",content:"역전식자재 홈페이지가 오픈했습니다.",date:"2026-09-23"},
{title:"역전식자재 수입 보고",content:"매출 200,000만원<br>원자재비 0만원<br>세금 34,000만원<br>인건비 20,000만원<br>운송료 100,000만원<br>사업수입 46,000만원<br>소득세 적용 후 23,000만원<br>순수익률 11.5%",date:"2026-09-28"}
];

const products=[
"🥬 배추 — 5,000원 — 1마리",
"🍎 사과 — 8,000원 — 1마리",
"🐟 고등어 — 7,000원 — 1마리",
"🦑 오징어 — 30,000원 — 1마리",
"🐙 낙지 — 1,000원 — 1마리"
];

function showPosts(list=posts){
postList.innerHTML=list.map(p=>`
<div class="post">
<b>${p.title}</b><p>${p.content}</p><small>${p.date}</small>
</div>`).join("");
}

function searchPosts(){
let w=postSearch.value.toLowerCase();
showPosts(posts.filter(p=>
p.title.toLowerCase().includes(w)||
p.content.toLowerCase().includes(w)));
}

function showProducts(list=products){
productList.innerHTML=list.map(p=>`
<div class="product">${p}</div>`).join("");
}

function searchProducts(){
let w=productSearch.value.toLowerCase();
showProducts(products.filter(p=>p.toLowerCase().includes(w)));
}

showPosts();
showProducts();

function initMap(){
const location={lat:37+34/60+7.51/3600,lng:126+40/60+37.12/3600};

const map=new google.maps.Map(document.getElementById("map"),{
center:location,zoom:17
});

const marker=new google.maps.Marker({
position:location,
map:map,
title:"역전식자재",
icon:{url:"마크.jpg",scaledSize:new google.maps.Size(50,50)}
});

new google.maps.InfoWindow({
content:"<b style='color:#198754'>역전식자재</b>"
}).open(map,marker);
}
</script>

<script async defer
src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCMWfqewJhJcRSIjWrfpRYINoYgmN6w79E&callback=initMap">
</script>

</body>
</html>
