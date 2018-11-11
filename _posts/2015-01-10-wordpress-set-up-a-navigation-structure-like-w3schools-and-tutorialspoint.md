---
ID: 263
post_title: 'WordPress &#8211; Set up a navigation structure like w3schools and tutorialspoint'
author: sher
post_excerpt: ""
layout: post
permalink: >
  https://codingbee.net/tutorials/wordpress/wordpress-set-up-a-navigation-structure-like-w3schools-and-tutorialspoint
published: true
post_date: 2015-01-10 00:00:00
---
If you have a collection of posts that you want your viewers to read in a certain sequence, then you need the following navigational features:
<ol>
 	<li>You need a navigation menu in the side column, which lists all the posts in the correct order</li>
 	<li>The navigation menu should only be visible when a viewer is on a post that is part of the series</li>
 	<li>There needs to be a "Previous" and "Next" links at the bottom of each post that allows the viewer to visit adjacent posts in the series.</li>
 	<li>The current post that a viewer is reading should appear as bold in the custom menu so that the viewer can where they are in the series</li>
</ol>
A lot of tutorials websites such as w3schools and tizag (as well as this website, codingBee) have all the above features, so that a viewer can follow a series of posts (i.e. tutorials) in sequence. Here's the good news, in Wordpress you can implement the same navigation structure in your website too! There are 2 parts you need set up, the functionality and the cs styling:
<h2>Setting up the functionality</h2>
<ol>
 	<li><strong>Ensure you have side bar enabled</strong> - This is where your post-series's navigation menu will appear.</li>
 	<li><strong>Write your Wordpress posts</strong> - if you haven't done already done so.</li>
 	<li><strong>Add your posts to a category</strong> - All the posts that are going to be in the same series needs to be assigned to the same category. You can also tag your posts in the usual way.</li>
 	<li><strong>Create a new Custom menu</strong> - This menu's name must be the same name as your category's name.</li>
 	<li><strong>Add your posts to the custom menu</strong> - Make sure you only add posts that belongs to the said category. Tip: you might need to enable posts visibility under screen options, otherwise you won't be able to add any posts to the custom menu.</li>
 	<li><strong>Arrange the order of your posts</strong> - this is just a case of dragging and dropping the menu items within the custom menu. This is part where we are ordering the posts.</li>
 	<li><strong>Install the <a href="https://en-gb.wordpress.org/plugins/widget-options/" target="_blank" rel="nofollow noopener">https://en-gb.wordpress.org/plugins/widget-options/</a></strong> - We'll be using this plugin to ensure that the navigation menu is only visible when the viewer is viewing a post that is listed under the said menu</li>
 	<li><strong>Add the custom menu to the side bar</strong> - you do this via Appearance-&gt;Widgets. Then drag the custom menu widget into the side bar. Then give the menu any title you like, and then select the menu from the dropdown list</li>
 	<li><strong>Select "Show on Checked Pages" from the dropdown list</strong> - This is still part of configuring the widget.</li>
 	<li><strong>Place a tick beside the said category's name</strong> - This will now ensure that the menu is only visible when the viewer is browsing the series of posts. Then save your changes.</li>
 	<li><strong>Install our plugin which is called <a href="https://wordpress.org/plugins/custom-menu-driven-prevnext-links/">Custom Menu Driven Prev/Next Links plugin</a></strong></li>
 	<li><strong>Insert function tag into your theme</strong> - Now comes a tricky part, go to Appearance -&gt; Editor. Here you need to insert:

[php light="true"]&amp;lt;?php CMD_prev_next('Tutorial'); ?&amp;gt;[/php]

...into one of the php files (which are listed on the far right). Exactly which file you need to edit depends on your particular WordPress theme. A good starting point could be going to a file called "single.php". Also, you can change the word "Tutorial" to something more appropriate for your website. For example if your website is about cooking, then you might want to change this to something like "Recipe". Then your previous/next links will display as "Previous Recipe" and "Next Recipe" respectively. After you have done this your posts in the series should now start displaying previous/next links that correspond to your new navigation menu. However the styling of the previous next links may look all wrong. We will fix this in the next step.</li>
</ol>
<h2>Setting up the CSS styling</h2>
At this point you should have the previous next links showing up at the bottom of your posts. But at this point, they may not look like how you want them to. You can fix this by adding some css styling. We recommend the following way to add the css styling:
<ol>
 	<li><strong>Install the <a href="https://wordpress.org/plugins/simple-custom-css/">Simple Custom CSS plugin</a></strong> - note if you are using wordpress 4.7+, then I don't think you need to use this plugin, because: <a href="https://wptavern.com/a-preview-of-the-custom-css-editor-added-to-the-customizer-in-wordpress-4-7">https://wptavern.com/a-preview-of-the-custom-css-editor-added-to-the-customizer-in-wordpress-4-7</a></li>
 	<li><strong>Apply custom CSS</strong> - Go to <code>Appearance -&gt; Custom CSS</code>. Then write the css code to customise how your Previous/Next links look. The css classes and ids that you need add styles to all have the "cmd" prefix in their name. To get you started, you can try to copy and paste in the following css code:
<pre>/* Previous-Next links */

.cmd_post_nav li { 
  list-style: none;
}

.cmd_post_nav .cmd_previous { 
  float:left;
  width: 48%;
}

.cmd_post_nav .cmd_next { 
  float:right;
  width: 48%;
  text-align: right;
}

.cmd_post_nav a {
  font-size: 16px;
  font-weight: 300;
}

.cmd_previous .meta-nav {
  float: left;
  font-size: 44px;
  font-weight: 500;
  line-height: 30px;
  padding-right: 10px;
}

.cmd_next .meta-nav {
  float: right;
  font-size: 44px;
  font-weight: 500;
  line-height: 30px;
  padding-left: 10px;
}

.cmd_right {
    float: right;
    width: 85%;
    word-wrap: break-word;
}

.cmd_left {
    float: left;
    width: 85%;
    word-wrap: break-word;
}

</pre>
</li>
 	<li><strong>Apply styling to currently active menu item</strong> - You may also want to add some more css styling to the currently selected item in the custom menu, so to make it standout from the rest. This is helpful for the viewer to see how far along they are in the series. To be do this all you need to do is add the following:
<pre>/* Current custom menu item styling */
.current-menu-item &gt; a {
   color:red;
   font-weight: bold;
}
</pre>
</li>
</ol>
Hopefully that helps. If you have any problems with implementing this solution, then please <a href="http://codingbee.net/contact/">contact us</a> and for a nominal fee of $15, we will insert the php tag into your theme, and apply a basic css styling to get you started.