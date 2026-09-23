
<html>
<head>
<meta charset="UTF-8">
<title>역전식자재</title>

<style>
body{margin:0;font-family:Arial,sans-serif;background:#f5f5f5;color:#222}
header{background:#087443;color:white;padding:20px;text-align:center}
nav{text-align:center;background:white;padding:15px}
nav a{margin:10px;color:#087443;text-decoration:none}
section{max-width:900px;margin:25px auto;padding:25px;background:white;border-radius:10px}
button{padding:10px;background:#087443;color:white;border:0;border-radius:5px}
input{padding:10px;width:60%}
.product{display:inline-block;background:#eee;padding:20px;margin:5px;border-radius:8px}
footer{background:#222;color:white;text-align:center;padding:20px}
</style>
</head>

<body>

<header>
<h1>역전식자재</h1>
<p>좋은 식자재, 합리적인 가격</p>
</header>

<nav>
<a href="#products">상품</a>
<a href="#donation">기부금</a>
<a href="#contact">문의</a>
</nav>

<section id="products">
<h2>상품</h2>

<input id="search" placeholder="상품 검색">
<button onclick="search()">검색</button>

<div id="list">

<div class="product">🥬 배추<br>5,000원</div>
<div class="product">🍎 사과<br>8,000원</div>
<div class="product">🐟 고등어<br>7,000원</div>
<div class="product">🍚 쌀<br>30,000원</div>

</div>
</section>

<section id="donation">
<h2>기부금 사용내역</h2>

<p>2026.01.10 지역사회 지원 - 100,000원</p>
<p>2026.03.15 취약계층 지원 - 200,000원</p>

</section>

<section id="contact">
<h2>문의</h2>

<p>📞 010-2694-6608</p>

<a href="https://forms.gle/XjKdz4JP4Co27bUNA"
   target="_blank">
<button>구글폼 문의하기</button>
</a>

</section>

<footer>
© 2026 역전식자재
</footer>

<script>
function search(){
  let q=document.getElementById("search").value.toLowerCase();
  document.querySelectorAll(".product").forEach(p=>{
    p.style.display=p.innerText.toLowerCase().includes(q)
      ?"inline-block":"none";
  });
}
</script>

</body>
</html>
```
