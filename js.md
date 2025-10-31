# 🚀 Helper Scripts & Shortcodes

Reusable utility functions and dynamic shortcodes to simplify content customization and interactive behavior across HubSpot pages.

---

## Sticky Header

```js
// Hide Header on on scroll down
var didScroll;
var lastScrollTop = 0;
var delta = 5;
var navbarHeight = $('header').outerHeight();
var duration = 0.4;
var scaleSpeed = 0.05;

var scene = new THREE.Scene();

var camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000);
camera.position.set(0, 0, 250);

var renderer = new THREE.WebGLRenderer({
   antialias: true,
   alpha: true
});
renderer.setClearColor(0x000000, 0);
renderer.setSize(1000, 1000);

var canvasElement = renderer.domElement;
canvasElement.style.background = 'none';
document.querySelector('.managment_right_inner_child').appendChild(canvasElement);

var group = new THREE.Object3D();

var radius = 120;
var separation = 1000;
var dotRadius = 1;
var dotSegments = 32;
var totalDots = 400;

for (var i = 0; i < totalDots; i++) {
   var radianS = Math.acos(2 * Math.random() - 1);
   var radianT = Math.random() * 2 * Math.PI;

   var pX = radius * Math.sin(radianS) * Math.cos(radianT);
   var pY = radius * Math.sin(radianS) * Math.sin(radianT);
   var pZ = radius * Math.cos(radianS);

   var randomOpacity = Math.random() * 0.5 + 0.5;
   var material = new THREE.MeshBasicMaterial({
      color: new THREE.Color(Math.random(), Math.random(), Math.random()),
      transparent: true,
      opacity: randomOpacity
   });

   var isBlurred = Math.random() > 0.8; // 20% chance of being blurred
   if (isBlurred) {
      var geometry = new THREE.SphereGeometry(dotRadius * 1.5, dotSegments * 2, dotSegments * 2);
      material.opacity = 0.2; // Adjust opacity for blurred dots

      var mesh = new THREE.Mesh(geometry, material);
      mesh.position.set(pX, pY, pZ);
      mesh.velocity = new THREE.Vector3(
         (Math.random() - 0.5) * 0.05,
         (Math.random() - 0.5) * 0.05,
         (Math.random() - 0.5) * 0.05
      );

      mesh.originalScale = mesh.scale.clone(); // Store the original scale
      mesh.isScaling = true; // Initialize scaling animation

      group.add(mesh);
   } else {
      var geometry = new THREE.SphereGeometry(dotRadius, dotSegments, dotSegments);
      var mesh = new THREE.Mesh(geometry, material);
      mesh.position.set(pX, pY, pZ);
      mesh.velocity = new THREE.Vector3(
         (Math.random() - 0.5) * 0.05,
         (Math.random() - 0.5) * 0.05,
         (Math.random() - 0.5) * 0.05
      );
      group.add(mesh);
   }
}

scene.add(group);

var mouseX = 0;
var mouseY = 0;

document.addEventListener('mousemove', (event) => {
   mouseX = (event.clientX / window.innerWidth) * 2 - 1;
   mouseY = -(event.clientY / window.innerHeight) * 2 + 1;
});

group.scale.set(0, 0, 0);

function update() {
   if (group.scale.x < 1) {
      group.scale.x += scaleSpeed;
      group.scale.y += scaleSpeed;
      group.scale.z += scaleSpeed;
   }

   group.children.forEach(child => {
      child.position.add(child.velocity.multiplyScalar(duration));
      if (child.position.length() > radius) {
         child.position.sub(child.velocity);
      }
   });

   var rotateX = group.rotation.x + 0.0002 * duration;
   var rotateY = group.rotation.y + 0.0005 * duration;
   var rotateZ = group.rotation.z + 0.001 * duration;
   group.rotation.set(rotateX, rotateY, rotateZ);

   group.rotation.y += (mouseX - group.rotation.y) * 0.08 * duration;
   group.rotation.x += (mouseY - group.rotation.x) * 0.08 * duration;

   group.children.forEach(child => {
      if (child.isScaling) {
         var scaleFactor = Math.random() > 0.5 ? 1.01 : 0.99; // Slightly larger or smaller
         child.scale.copy(child.originalScale).multiplyScalar(scaleFactor);
      }
   });

   camera.lookAt(scene.position);
   renderer.clear();
   renderer.render(scene, camera);

   requestAnimationFrame(update);
}

update();
$(window).scroll(function (event) {
   didScroll = true;
});

setInterval(function () {
   if (didScroll) {
      hasScrolled();
      didScroll = false;
   }
}, 250);

function hasScrolled() {
   var st = $(this).scrollTop();

   // Make sure they scroll more than delta
   if (Math.abs(lastScrollTop - st) <= delta)
      return;

   // If they scrolled down and are past the navbar, add class .nav-up.
   // This is necessary so you never see what is "behind" the navbar.
   if (st > lastScrollTop && st > navbarHeight) {
      // Scroll Down
      $('header').removeClass('nav-down').addClass('nav-up');
   } else {
      // Scroll Up
      if (st + $(window).height() < $(document).height()) {
         $('header').removeClass('nav-up').addClass('nav-down');
      }
   }

   lastScrollTop = st;
}
```

## view in element
```js
if ($(this).isInViewport()) {} else {}

$.fn.isInViewport = function () {
   var elementTop = $(this).offset().top;
   var elementBottom = elementTop + $(this).outerHeight();

   var viewportTop = $(window).scrollTop();
   var viewportBottom = viewportTop + $(window).height();

   return elementBottom > viewportTop && elementTop < viewportBottom;
};
```


## Out-of-Bounds Element Debugger
A debugging utility that scans all elements on the page and visually highlights any that extend beyond the visible viewport. It outlines problematic elements in red and logs them in the console for quick inspection.

```js
(function (debug) {
   var w = debug.documentElement.offsetWidth,
      t = debug.createTreeWalker(debug.body, NodeFilter.SHOW_ELEMENT),
      b;
   while (t.nextNode()) {
      b = t.currentNode.getBoundingClientRect();
      if (b.right > w || b.left < 0) {
         t.currentNode.style.setProperty('outline', '1px dotted red', 'important');
         console.log(t.currentNode);
      }
   };
}(document));
```

## Fancybox js
**With CDN**
```js
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/fancybox/3.3.5/jquery.fancybox.min.css" /> 
<script src="https://cdnjs.cloudflare.com/ajax/libs/fancybox/3.3.5/jquery.fancybox.min.js"></script>

$(".fancy-box").each(function (e) {
   $(this).find(".fancy-link").attr("data-src", "#fancy-" + e);
   $(this).find(".fancy-model").attr("id", "fancy-" + e);
});

----------------------- Iframe Autoplay and stop ----------------------- 
$("[data-fancybox]").fancybox({
   type: "inline",
   afterLoad: function (instance, current) {
      var iframe = current.$content.find("iframe")[0];
      if (iframe) {
         iframe.src = iframe.src.replace("autoplay=false", "autoplay=true");
      }
   },
   beforeClose: function (instance, current) {
      var iframe = current.$content.find("iframe")[0];
      if (iframe) {
         iframe.src = iframe.src.replace("autoplay=true", "autoplay=false");
      }
   }
});

```

## Counter Up / CounterUp
```js
<script src="//cdnjs.cloudflare.com/ajax/libs/waypoints/2.0.3/waypoints.min.js"></script> 
<script src="https://cdnjs.cloudflare.com/ajax/libs/Counter-Up/1.0.0/jquery.counterup.min.js"></script> 
$('.counter').counterUp({ 
    delay: 10, 
    time: 3000 
});
```

## Isotop filter
```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/jquery.isotope/3.0.4/isotope.pkgd.min.js"></script> 
var $container = $('.work-portfilio'),
   $filterLinks = $('.work-tab a');

$container.isotope({
   itemSelector: '.single-work',
   filter: '*'
});

$filterLinks.click(function () {
   var $this = $(this);
   // don't proceed if already selected 
   if ($this.hasClass('selected')) {
      return;
   }

   $filterLinks.filter('.selected').removeClass('selected');
   $this.addClass('selected');

   // get selector from data-filter attribute 
   selector = $this.data('filter');
   $container.isotope({
      filter: selector
   });
});
```


## Scroll to Link Click to scroll link rel-anchor
```js
$('a[rel="rel-anchor"], a[href*="#"]:not([href="#"]) ').click(function() { 
    var id = $(this).attr('href'); 
    if($(id).length > 0){ 
      $('html, body').animate({ 
        scrollTop: $($.attr(this, 'href')).offset().top 
      }, 500); 
      return false; 
    } 
});
```

## Equal height jQuery
```js
equalheight = function(container) { 
  var currentTallest = 0, 
    currentRowStart = 0, 
    rowDivs = new Array(), 
    $el, 
    topPosition = 0; 
  $(container).each(function() { 
    $el = $(this); 
    $($el).height('auto') 
    topPostion = $el.position().top; 
    if (currentRowStart != topPostion) { 
      for (currentDiv = 0; currentDiv < rowDivs.length; currentDiv++) { 
        rowDivs[currentDiv].height(currentTallest); 
      } 
      rowDivs.length = 0; // empty the array 
      currentRowStart = topPostion; 
      currentTallest = $el.height(); 
      rowDivs.push($el); 
    } else { 
      rowDivs.push($el); 
      currentTallest = (currentTallest < $el.height()) ? ($el.height()) : (currentTallest); 
    } 
    for (currentDiv = 0; currentDiv < rowDivs.length; currentDiv++) { 
      rowDivs[currentDiv].height(currentTallest); 
    } 
  }); 
}
```


## Slick Slider
```js
<link rel="stylesheet" type="text/css" href="https://cdnjs.cloudflare.com/ajax/libs/slick-carousel/1.6.0/slick.min.css"> 
<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/slick-carousel/1.6.0/slick.min.js"></script>

$('.multiple-items').slick({  
  infinite: true,  
  slidesToShow: 3,  
  slidesToScroll: 3 
}); 

$('.responsive').slick({ 
  dots: true, 
  infinite: false, 
  speed: 300, 
  slidesToShow: 4, 
  slidesToScroll: 4, 
  responsive: [ 
    { breakpoint: 992, settings: { slidesToShow: 3, slidesToScroll: 3 } }, 
    { breakpoint: 768, settings: { slidesToShow: 2, slidesToScroll: 2 } }, 
    { breakpoint: 501, settings: { slidesToShow: 1, slidesToScroll: 1 } } 
  ] 
});

/*------------------------------------- Slick Slider with Custom Pagination -----------------------*/
$('.marketing-slider').slick({ 
  arrows: true, 
  dots: true, 
  infinite: false, 
  nextArrow:'.prev-arrow', 
  prevArrow:'.next-arrow', 
  customPaging: function(slider, i) { 
    var iconControl = $(slider.$slides[i]).find('.dots-contain'); 
    return iconControl; 
  } 
});

/*------------------------------------- slick Slider set Position -------------------------------------*/ 
$('.responsiveOne').slick('setPosition', 0);

/*------------------------------------- Slick slider with number and index -----------------------------*/
var $status_2 = $('.pagingInfo_2'), 
    $slickElement_2 = $('.feature-box-slider'); 
$slickElement_2.on('init reInit afterChange', function(event, slick, currentSlide, nextSlide) { 
  var i = (currentSlide ? currentSlide : 0) + 1; 
  $status_2.text(i + ' of ' + slick.slideCount); 
});
```


## Active Scroll Link / Scroll to active Link
```js
$(document).on("scroll", onScroll); 
function onScroll(event) { 
  var scrollPos = jQuery(document).scrollTop(); 
  jQuery('.header .main-menu-wrapper ul.mainmenu li.menu-item-home').each(function() { 
    var currLink = jQuery(this); 
    var refElement = jQuery(currLink.attr("href")); 
    var has_class = jQuery(this).find("a").attr("href"); 
    has_class = has_class.split('#'); 
    has_class = has_class[has_class.length - 1]; 
    header = jQuery(".header").outerHeight(); 
    if (jQuery("#" + has_class).length > 0) { 
      var top_pos = jQuery("#" + has_class).offset().top; 
      if (jQuery("#" + has_class).offset().top - header <= scrollPos && jQuery("#" + has_class).offset().top + jQuery("#" + has_class).height() - header > scrollPos) { 
        jQuery('.header .main-menu-wrapper ul.mainmenu li.menu-item-home a').removeClass("active"); 
        currLink.addClass("active"); 
      } else { 
        currLink.removeClass("active"); 
      } 
    } 
  }); 
}
```

## Same height
```js
function sameheight(clsaaName) {
   var highest = null;
   var hi = 0;
   jQuery(clsaaName).each(function () {
      var h = jQuery(this).outerHeight();
      if (h > hi) {
         hi = h;
         highest = jQuery(this);
      }
   });
   jQuery(clsaaName).css('min-height', hi);
}

sameheight('.two_perchantage .service .sma_height')
```

## Scrooling Enabled and Disabled
```js
var disableScroll = false; 
var scrollPos = 0; 
function stopScroll() { 
    disableScroll = true; 
    scrollPos = $(window).scrollTop(); 
} 
function enableScroll() { 
    disableScroll = false; 
} 
$(function(){ 
    $(window).bind('scroll', function(){ 
         if(disableScroll) $(window).scrollTop(scrollPos ); 
    }); 
    $(window).bind('touchmove', function(){ 
         $(window).trigger('scroll'); 
    }); 
});
```

## Accordion Faq  acc1
```js
if (window.matchMedia("(min-width:992px)").matches) { 
  $(".accordion-title").on("click", function() { 
      $(this).toggleClass("active"); 
      $(this).next(".accordion-text").slideToggle(250); 
      $(".accordion-title").not($(this)).removeClass("active"); 
      $(".accordion-text").not($(this).next()).slideUp(250) 
  }); 
} 
if (window.matchMedia("(max-width:991px)").matches) { 
    $(".accordion-title").on("click", function() { 
        var b = $(this); 
        $(this).toggleClass("active"); 
        $(this).next(".accordion-text").slideToggle(250); 
        $(".accordion-title").not($(this)).removeClass("active"); 
        $(".accordion-text").not($(this).next()).slideUp(250); 
        setTimeout(function() { 
            $("html, body").animate({ 
                scrollTop: b.offset().top //- $(".header").outerHeight() 
            }, 500) 
        }, 300) 
    }); 
}
```

## Tabbing
**HTML**
```html
 <div class="tab-section"> 
	<div class="tab"> 
		<div class="tab-list"> 
			<ul> 
				<li></li> 
			</ul> 
		</div> 
		<div class="tab-content-list"> 
			<div class="tab-content"> 
				<div class="title"> 
				</div> 
				<div class="inner"> 
				</div> 
			</div> 
		</div> 
	</div> 
</div>
```

**JS**
```js
var Loadflag = false;
var flag = false;
if (window.matchMedia("(max-width:767px)").matches) {
   flag = true;
}
$('.tab-section .tab-list ul li').on('click', function (e) {
   e.preventDefault();
   var index = $(this).index(),
      tab = $(this).parents('.tab'),
      tabList = tab.find('.tab-content-list');
   tabList.find('.tab-content').eq(index).fadeIn();
   tabList.find('.tab-content').not(':eq(' + index + ')').hide();
   $(this).addClass('active').siblings().removeClass('active');
});
$('.tab-section .title').on('click', function () {
   var tab = $(this).parent('.tab-content');
   tab.find('.inner').slideToggle(300);
   tab.siblings().find('.inner').slideUp(300);
   tab.toggleClass('active').siblings().removeClass('active');
   if (flag && Loadflag) {
      setTimeout(function () {
         $("html, body").animate({
            scrollTop: tab.offset().top
         }, 500)
      }, 500)
   }
});
if (flag) {
   $(".tab-section .tab-list").hide();
   $('.tab-section .title').eq(0).trigger('click');
} else {
   $('.tab-section .title').hide();
   $(".tab-section .tab-list ul li:nth-child(1)").trigger('click');
}
$(window).on('load', function () {
   Loadflag = true;
});
```


## Navigation active base on url
```js
$(window).on('load', function () {
   var url = window.location.href;
   clickToScroll(url);

   jQuery('.row-fluid .menu .hs-menu-wrapper a').click(function (e) {
      var href = jQuery(this).attr('href');
      clickToScroll(href);
   });
});

function clickToScroll(url) {
   console.log('url ', url)
   if (url.indexOf('#') > 0) {
      url = url.split('#');
      url = url[url.length - 1];
      if (url.indexOf('?') > 0) {
         url = url.split('?')[0];
      }
      console.log('url filted ', url)
      if (url) {
         var id = '#' + url;
         if (jQuery('div[data-id="' + id + '"]').length > 0) {

            jQuery('html, body').animate({
               scrollTop: jQuery('div[data-id="' + id + '"]').offset().top
            }, 500);
            return false;
         }
      }
   }
}
```


## Multi Tabbing
```js
/* Tabbing */

var flag = false;

if (window.matchMedia("(max-width:991px)").matches) {
   flag = true;
}


$('.tab-section .tab-list ul li').on('click', function (e) {
   e.preventDefault();
   var index = $(this).index(),
      tab = $(this).parents('.tab'),
      tabList = tab.find('.tab-content-list');

   tabList.find('.tab-content').eq(index).fadeIn();
   tabList.find('.tab-content').not(':eq(' + index + ')').hide();

   $(this).addClass('active').siblings().removeClass('active');
});

$('.tab-section .title').on('click', function () {
   var tab = $(this).parent('.tab-content');
   tab.find('.inner').slideToggle(300);
   tab.siblings().find('.inner').slideUp(300);
   tab.toggleClass('active').siblings().removeClass('active');
   if (flag && Loadflag) {
      setTimeout(function () {
         $("html, body").animate({
            scrollTop: tab.offset().top
         }, 500)
      }, 500)
   }
});

if (flag) {
   $(".tab-section .tab-list").hide();
   $('.tab-section .title').eq(0).trigger('click');
} else {
   $('.tab-section .title').hide();
   $(".tab-section .tab-list ul li:nth-child(1)").trigger('click');
}
```

## Load on scroll #id with url
```js
$(window).on('load',function() {
  var url = window.location.hash; 
    if (url.length) {
      $('html,body').animate({
        scrollTop: $(url).offset().top - 100
      }, 1000);
    }
});
```
