### 1. 参考[object-fit和object-position](https://www.w3cplus.com/css3/on-object-fit-and-object-position.html);
       资料：[cropping images in css with object-fit](https://alligator.io/css/cropping-images-object-fit/);
### 2. 视频做背景样例
    <pre>
	    <code>
			 //div布局
			 <div class="devsite-landing-row-item-description-content" style="position: relative;">
				<video autoplay="" loop="" muted="" preload=""
					   poster="/machine-learning/crash-course/images/landing-hero.png"
					   class="mlcc-landing-hero-video">
					<source src="https://developers.google.cn/machine-learning/crash-course/images/landing-hero-video.webm" type="video/mp4">
				</video>
				<p class="course-description">Google 制作的节奏紧凑、内容实用的机器学习简介课程</p>
			 </div>
			 //css 样式
			 .mlcc-landing-hero-video {
				height: auto;
				left: 0;
				min-height: 100%;
				min-width: 100%;
				object-fit: cover;
				position: absolute;
				top: 0;
				width: auto;
				z-index: -1;
			}
			 
		</code>
	</pre>
