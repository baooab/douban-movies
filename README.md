# douban-movies
豆瓣电影 Top 250

---

使用豆瓣开放 API(https://api.douban.com/v2/movie/top250) 写成的。

以下是全部代码：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <meta name="description" content="">
    <meta name="author" content="">
    <title>豆瓣电影Top250</title>
    <style>
        body {
            font-family: Georgia, serif;
            background-color: #f5f5d5;
            word-spacing: 0.2em;
            font-size: .7em;
            padding: .7em;
            text-shadow: 0 1px 1px #ccc;
        }

        hr {
            margin: .7em auto;
            border-width: 1px;
            border-color: black;
            border-style: dashed;
            border-bottom: none;
        }

        strong {
            color: green;
        }

        hr.placeholder {
            border-color: #fff;
            clear: both;
        }

        .zb-container {
            position: relative;
        }

        .zb-float-div {
            display: inline-block;
            float: left;
            margin: 1px;
        }

        .zb-float-div:nth-of-type(2n) {
            background-color: rgba(0,0,0,.1);
        }

        .zb-pagination {
            display: inline-block;
            list-style: none;
            padding: 0;
            margin: 0;
        }

        .zb-pagination li{
            display: inline-block;
            padding: .03em;
        }

        footer {
            margin-top: 1.2em;
            color: grey;
        }

        a {
            text-decoration: none;
            color: #000;
        }

        a:hover {
            text-decoration: underline;
        }

        ul > li.active, ul > li:hover {
            background-color: pink;
            border-radius: 50%;
            font-size: 1.4em;
        }

    </style>

  </head>

  <body class="container">

       <header id="page-header"></header>

       页数→
       <ul class="zb-pagination">
            <li><a href="?start=0">1</a></li>
            <li><a href="?start=20">2</a></li>
            <li><a href="?start=40">3</a></li>
            <li><a href="?start=60">4</a></li>
            <li><a href="?start=80">5</a></li>
            <li><a href="?start=100">6</a></li>
            <li><a href="?start=120">7</a></li>
            <li><a href="?start=140">8</a></li>
            <li><a href="?start=160">9</a></li>
            <li><a href="?start=180">10</a></li>
            <li><a href="?start=200">11</a></li>
            <li><a href="?start=220">12</a></li>
            <li><a href="?start=240">13</a></li>
       </ul>

       <hr>

       <main id="page-main">加载中...</main>

       <hr class="placeholder"><hr>

       页数→
       <ul class="zb-pagination">
            <li><a href="?start=0">1</a></li>
            <li><a href="?start=20">2</a></li>
            <li><a href="?start=40">3</a></li>
            <li><a href="?start=60">4</a></li>
            <li><a href="?start=80">5</a></li>
            <li><a href="?start=100">6</a></li>
            <li><a href="?start=120">7</a></li>
            <li><a href="?start=140">8</a></li>
            <li><a href="?start=160">9</a></li>
            <li><a href="?start=180">10</a></li>
            <li><a href="?start=200">11</a></li>
            <li><a href="?start=220">12</a></li>
            <li><a href="?start=240">13</a></li>
       </ul>

       <footer id="page-footer">(c) 2016 张宝制作</footer>

    <script src="http://lib.sinaapp.com/js/jquery/1.9.1/jquery-1.9.1.min.js"></script>
    <script>
(function () {

    "use strict";

    var start = getParameterByName('start');

    handleGetJSON(start);

    function handleGetJSON(start) {
        let url = 'https://api.douban.com/v2/movie/top250?callback=?';
        if (start) {
            url = url + '&start=' + start;
        }

        $.ajaxSettings.async = false;
        $.getJSON(url)
            .done(function (movies) {
                renderMovies(handleMovies(movies));
            })
            .fail(function (err) {
                console.log(err.statusText);
            });
    }

    function handleMovies(movies) {
        var headerTmpl = `<strong>${movies.title}</strong> / 共${movies.count}部电影，从第${movies.start + 1}部电影开始显示，一共${movies.total}部电影。`;
        var contentTmpl = movies => `
          ${movies.subjects.map((movie, index) => `
            <div class="zb-float-div">
               <strong>${start + index + 1}</strong> <a href="${movie.alt}" target="_blank">${movie.title} ${movie.original_title}（${movie.year}）</a><br>
               导演：<a href="${movie.directors[0].alt}" target="_blank">${movie.directors[0].name}</a><br>
               主演：<a href="${movie.casts[0].alt}" target="_blank">${movie.casts[0].name}</a><br>
               类型：${movie.genres.join(" / ")}<br>
               评分：${movie.rating.average}
            </div>
          `).join("")}
        `;
        contentTmpl = contentTmpl(movies);
        return { contentTmpl, headerTmpl };
    }

    function renderMovies(moviesData) {
        document.getElementById('page-header').innerHTML = moviesData.headerTmpl;
        document.getElementById('page-main').innerHTML = moviesData.contentTmpl;
    }

    function renderError() {
        document.getElementById('page-main').innerHTML = '数据获取失败:(';
    }

    function getParameterByName(name, url) {
        if (!url) {
          url = window.location.href;
        }
        name = name.replace(/[\[\]]/g, "\\$&");
        var regex = new RegExp("[?&]" + name + "(=([^&#]*)|&|#|$)"),
            results = regex.exec(url);
        if (!results) return null;
        if (!results[2]) return '';
        return decodeURIComponent(results[2].replace(/\+/g, " "));
    }

    start = start ? parseInt(start) : 0;
    let page = parseInt(start / 20 + 1);
    let selector = "ul li:nth-child(" + page +")";
    $(selector).addClass('active');
})();


    </script>
  </body>
</html>
```
