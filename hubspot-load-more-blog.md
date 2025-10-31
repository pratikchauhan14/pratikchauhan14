# 🧩 HubSpot Blog – Load More (AJAX Based)

Load additional blog posts dynamically without reloading the page using HubSpot’s Blog API or HubL partials.

---

## 1. load more for dynamic blog

**HTML**
```twig
<div class="load_more_wrapper" style="text-align:center;">
   <a href="javascript:;" class="load-more hs-button">
   <span>Load More</span>
   <span>Loading...</span>
   </a>
</div>
```

**CSS**
```twig
<style>
.blog-item { -webkit-animation: scale-in-center 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both; animation: scale-in-center 0.5s cubic-bezier(0.250, 0.460, 0.450, 0.940) both; }
@-webkit-keyframes scale-in-center {
  0% { -webkit-transform: scale(0); transform: scale(0); opacity: 1 }
  100% { -webkit-transform: scale(1); transform: scale(1); opacity: 1 }
}
@keyframes scale-in-center {
  0% { -webkit-transform: scale(0); transform: scale(0); opacity: 1 }
  100% { -webkit-transform: scale(1); transform: scale(1); opacity: 1 }
}
a.load-more.active span:nth-child(1) { display: none; }
a.load-more span:nth-child(2) { display: none; }
a.load-more.active { pointer-events: none; opacity: 0.5; }
a.load-more.active span:nth-child(2) { display: block; }
a.load-more.none { display: none !important; }
.load_more_wrapper { padding-top: 40px; }
</style>
```

**JS**
```twig
<script>
  {% set total_pages = contents.total_page_count %} 
  var totalPage = {{ total_pages }}; 
  var res = 0; 
  var blogUrl = "{{group.absolute_url}}" 
  var currentPage = {{ next_page_num }}; 
  var lastPage = {{ last_page_num }}; 

  var fetching = false; 
  var blogOutOfPosts = false; 
  var postContainer = $('.post-listing'); 
  var currentUrl = window.location.href; 
  var topicTxt = '{{ topic }}'; 
  var authorTxt = '{{ author }}'; 
  var postLen = $('.blog-item').length; 

  if(currentPage <= 1) { 
	 $('.load-more').hide(); 
  } 

  if(lastPage == 1 ){ 
	 $('.load-more').hide(); 
  } 
  console.log("Total Page: " + totalPage); 
  $('.load-more').on('click', function(){ 
	 res = res + 1; 
	 //console.log('Current Page: '+currentPage); 
	 //console.log('total Page: '+totalPage); 
	 $(this).addClass('active'); 
	 if(blogOutOfPosts || fetching) 
		return; 
	 var postContainerOffset = postContainer.offset().top; 
	 var postContainerHeight = postContainer.height(); 
	 fetching = true; 
	 if(currentUrl.indexOf('tag/') != -1 && topicTxt) { 
		var ajaxUrl = blogUrl+"/tag/"+ topicTxt +"/page/"+currentPage; 
	 }else if(currentUrl.indexOf('author/') != -1 && authorTxt){ 
		var ajaxUrl = blogUrl+"/author/"+authorTxt+"/page/"+currentPage; 
	 }else { 
		var ajaxUrl = blogUrl+"/page/"+currentPage; 
	 } 
	 $(".load-more").css('display','inline-block'); 
	 setTimeout(function(){ 
		$.get(ajaxUrl, function(data){ 
		  var page = $(data); 
		  var posts = page.find('.blog-item'); 
		  if(posts.length == 0){ 
			 blogOutOfPosts = true; 
			 $(".load-more").css('display','none'); 
			 return; 
		  } 
		  posts.each(function(){ 
			 var blogPost = $(this); 
			 postContainer.append(blogPost).fadeIn(1000);; 
		  }); 
		  setTimeout(function(){ 
			 currentPage += 1; 
			 fetching = false; 
			 $(".blog-item").removeClass("hidden"); 
			 $(this).removeClass('active'); 
		  }, 1000); 
		  $('.load-more').removeClass('active'); 
		}); 
		if(totalPage <= currentPage ){ 
		  $('.load-more').addClass('none'); 
		} 
	 },500); 
  }); 
</script>
```
