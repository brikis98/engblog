LinkedIn Engineering Blog
=========================

The LinkedIn engineering blog is hosted on [Github Pages](http://pages.github.com/) and powered by [Jekyll](http://www.jekyllrb.com). 
The goal of this platform is to make blogging fun for engineers using the tools that they interact with everyday. 
Posts are written using Git flavored [Markdown](http://daringfireball.net/projects/markdown/) and processed into 
static content by Jekyll using [Liquid](http://liquidmarkup.org/) templates. After all is said and done the site is 
pushed to Github using the normal Git commit process.

###Getting Started

Steps:
* install [Ruby](http://www.ruby-lang.org/en/downloads/) 
* install the Rake gem
* clone the repo
* run **'rake install'** to set up the dependencies (use sudo if you do not have superuser access)
* run **'rake deploy'** to set up the environment (could take some time to build if there are a lot of posts)

Once your environment is set up it is time to start blogging!

###Blogging

There are three types of posts for the blog, and these are:

* Posts
* Projects
* Team Members
  * Applications
  * Data Analysis
  * Systems and Infrastructure

Each of these posts can be created by using the templates located in the **'source/_drafts/templates'** directory. 
There are also several template variations of each template available, each with different layouts for different 
purposes.

Steps for making a post:
* Make a copy of the template, naming it with the title of your post (hyphen separated).
* Edit the YAML variables with your information (make sure to fill in everything)
* Write your post
* Save it into the **'source/_drafts/'** folder (it'll stay here until it's approved)
* Continue writing as many posts as you want
* Commit the post(s) with **'rake push'**

In order to move blog posts from draft to production, simply alter the name putting today's date (YYYY-MM-DD) in front
of the title. Then move it into the appropriate folder within **'source/_posts'**


