<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>역전식자재</title>

<style>
body{font-family:Arial,sans-serif;margin:0;background:#f5f5f5;color:#222}
header{background:#087443;color:white;padding:20px;text-align:center}
nav{background:#fff;padding:12px;text-align:center}
nav a{margin:0 12px;text-decoration:none;color:#087443;font-weight:bold}
section{max-width:900px;margin:30px auto;background:white;padding:30px;border-radius:12px}
h2{color:#087443}
input{padding:10px;width:70%;max-width:500px}
button{padding:10px 16px;background:#087443;color:white;border:0;border-radius:5px}
.product{display:inline-block;width:200px;margin:10px;padding:20px;background:#eee;border-radius:10px}
footer{background:#222;color:white;text-align:center;padding:25px}
</style>
</head>

<body>

<header>
<h1>역전식자재</h1>
<p>신선한 식자재, 믿을 수 있는 역전식자재</p>
</header>

<nav>
<a href="#products">상품</a>
<a href="#donation">기부금 사용내역</a>
<a href="#contact">문의</a>
</nav>

<section>
<h2>검색</h2>
<input id="search" placeholder="상품명을 검색하세요">
<button onclick="searchProduct()">검색</button>
<p id="result"></p>
</section>

<section id="products">
<h2>상품</h2>

<div class="product">🥬 배추<br>5,000원</div>
<div class="product">🍎 사과<br>8,000원</div>
<div class="product">🐟 고등어<br>7,000원</div>
<div class="product">🍚 쌀<br>30,000원</div>
</section>

<section id="donation">
<h2>기부금 사용내역</h2>

<table border="1" width="100%" cellpadding="10">
<tr>
<th>날짜</th>
<th>사용처</th>
<th>금액</th>
</tr>

<tr>
<td>2026-01-01</td>
<td>지역사회 기부</td>
<td>100,000원</td>
</tr>

<tr>
<td>2026-02-01</td>
<td>취약계층 지원</td>
<td>200,000원</td>
</tr>
</table>
</section>

<section id="contact">
<h2>문의하기</h2>

<p><a href="tel:01026946608">010-2694-6608“>
<button>📞전화문의</button>
</a>
</p>

<p><a href="https://forms.gle/G9Bxju48dDDFhyMBA">
<button>📋구글폼으로 문의하기</button>
</a>
</p>

</section>

<footer>
© 2026 역전식자재. All rights reserved.
</footer>

<script>
function searchProduct(){
    let text=document.getElementById("search").value;
    document.getElementById("result").innerText =
        text ? "'" + text + "' 검색 결과를 확인하세요." : "검색어를 입력해주세요.";
}
</script>

</body>
</html>
