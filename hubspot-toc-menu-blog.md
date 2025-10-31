# 🧩 HubSpot Blog – Dynamic Blog TOC Menu

Automatically generates a clickable table of contents from your blog headings (H2, H3). Helps readers navigate long articles easily with smooth scrolling and active section highlighting.

---

## 1. TOC Menu

**HTML**
```twig
<div id="blog-headings" style="display: none">
 <div class="toc_icon">
  <div class="toc_open">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 640"><path d="M96 160C96 124.7 124.7 96 160 96L480 96C515.3 96 544 124.7 544 160L544 480C544 515.3 515.3 544 480 544L160 544C124.7 544 96 515.3 96 480L96 160zM160 160L160 224L224 224L224 160L160 160zM480 160L288 160L288 224L480 224L480 160zM160 288L160 352L224 352L224 288L160 288zM480 288L288 288L288 352L480 352L480 288zM160 416L160 480L224 480L224 416L160 416zM480 416L288 416L288 480L480 480L480 416z"/></svg>
  </div>
 </div>
 <div class="toc_panel">
  <div>
    <div class="toc_panel_title">Contents</div>
    <div id="blog-jumps"></div>
  </div>
  <div class="close_btn toc_icon">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 640"><path d="M183.1 137.4C170.6 124.9 150.3 124.9 137.8 137.4C125.3 149.9 125.3 170.2 137.8 182.7L275.2 320L137.9 457.4C125.4 469.9 125.4 490.2 137.9 502.7C150.4 515.2 170.7 515.2 183.2 502.7L320.5 365.3L457.9 502.6C470.4 515.1 490.7 515.1 503.2 502.6C515.7 490.1 515.7 469.8 503.2 457.3L365.8 320L503.1 182.6C515.6 170.1 515.6 149.8 503.1 137.3C490.6 124.8 470.3 124.8 457.8 137.3L320.5 274.7L183.1 137.4z"/></svg>
  </div>
 </div>
</div>
```
**CSS**
```twig
<style>
:root { --bg_color: #fff; --text_color: #000; --text_hover_color: #c9194a; }
html { scroll-behavior: smooth; }
.toc_icon.toc_icon-show .toc_open { opacity: 0; }
.toc_panel .close_btn { position: absolute; background: var(--text_hover_color); height: 30px; width: 30px; top: 0; opacity: 0; right: 0; display: flex; flex-flow: wrap; align-items: center; justify-content: center; cursor: pointer; }
.toc_panel .close_btn svg { height: 20px; width: 20px; fill: var(--bg_color); }
.close_btn.toc_icon.toc_icon-show { transform: none; opacity: 1; }
.toc_icon .toc_open { height: 40px; width: 40px; display: flex; align-items: center; justify-content: center; z-index: 9; border-radius: 10px; position: fixed; top: 40%; left: -10px; color: white; font-size: 32px; background: var(--bg_color); box-shadow: 0 0 10px 0 rgba(0, 0, 0, .25); transition: .5s all; }
.toc_open svg { height: 20px; width: 25px; }
.res-listing > div:first-child { min-width: 0; padding: 0; }
.toc_panel { color: var(--text_color); z-index: 10; width: 300px; position: fixed; left: 0; top: 40%; overflow: auto; max-height: calc(100vh - 120px); transition: .5s all; transform: translate3d(-300px, -40%, 0); background: white; padding: 35px 25px; border-radius: 0 10px 10px 0; box-shadow: 0 0 10px 0 rgba(0, 0, 0, .25); display: flex; justify-content: flex-end; }
.toc_panel-show { transform: translate3d(0, -40%, 0); }
.toc_panel-text { display: none; }
.blog-h-jump[data-header="h3"] { margin-left: 20px; font-size: 90%; line-height: 1.2; }
.toc_icon { cursor: pointer; }
div#blog-jumps { display: flex; flex-direction: column; gap: 9px; }
.blog-h-jump { transition: all 0.4s ease; cursor: pointer; color: var(--text_color); }
.blog-h-jump:hover { color: var(--text_hover_color); }
@media(max-width:767px) {
  .toc_panel { padding: 35px 25px; }
}
</style>
```

**JS**
```twig
<script>
  $(document).ready(function() {
	 const headers = $('.blog_post_body h1, .blog_post_body h2 , .blog_post_body h3');

	 if(headers.length) {
		let headersHTML = '';
		headers.each(function(i,item) {
		  const headerTag = item.tagName.toLowerCase();
		  headersHTML += `<div class="blog-h-jump" data-index="${i}" data-header="${headerTag}">${$(this).text()}</div>`
		});

		$('#blog-jumps').html(headersHTML);

		$(".blog-h-jump").click(function() {
		  $([document.documentElement, document.body]).animate({
			 scrollTop: $(headers[$(this).data('index')]).offset().top
		  }, 100, 'linear');
		});

		$('.toc_icon').click(function() {
		  $('.toc_icon').toggleClass('toc_icon-show');
		  $('.toc_panel').toggleClass('toc_panel-show');
		});

		$('#blog-headings').css('display', 'block');
	 }
  });
</script>
```
