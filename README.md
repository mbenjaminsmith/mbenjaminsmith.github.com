# RapidFire Studio's Website

## Built with Jekyll, Hosted on GitHub Pages

This is a non-standard GitHub Pages deployment with a custom plugin for pagination and a separate landing page and blog index.

To create a Jekyll site that works in the same manner, do the following (assuming OS X):

1. Create a public GitHub repo with the format <yourgithubusername>.github.com

2. Clone your new repo to your local machine

```bash
$ git clone git@github.com:<yourgithubusername>/<yourgithubusername>.github.com.git
$ cd <yourgithubusername>.github.com
```

3. Install Jekyll 

```bash
$ gem install jekyll
```

4. Create your first Jekyll site

```bash
$ mkdir _includes _layouts _posts css js img
$ touch robots.txt 404.html index.html blog.htmlx
$ touch _layouts/default.html _layouts/post.html css/style.css
$ touch _config.yml
$ echo auto: true >> _config.yml
```

5. Setup your default html scaffolding and style in `default.html` and `style.css`. This site uses the excellent [Gumby Framework](http://gumbyframework.com/)

6. Your `default.html` file should include a `{{ content }}` template tag in the body. This is where your page content will be rendered

7. Write your first post in the format YYYY-MM-DD-title.html. Markdown and other formats are available.

```bash
$ touch _posts/2013-01-16-my-first-post.html
```

8. Add the following to the top of your post, along with the text of your post

```bash
---
  layout: post
  title: My First Blog Post
---

<p>Lorem ... </p>
```

9. Update your `post.html` layout to tie everything together

```bash 
---
  layout: default
---

<div> {{ page.content }} </div>
```

10. Next update your `index.html` to act as your landing page and your `blog.html` to act as your blog index

```bash 
---
  layout: default
---

<put some html content here>
```

11. Jekyll's built in pagination won't work for non-standard deployments so we'll quickly hack the plugin to work for us

```bash
$ mkdir _plugins
$ cd $(gem which jekyll | xargs dirname)
$ cp ./jekyll/generators/pagination.rb ~/<yourwebsitedirectory>/_plugins/pagination_custom.rb
$ cd -
$ sed -i '' 's/index.html/blog.html/g' _plugins/pagination_custom.rb
$ echo paginate: 5 >> _config.yml # how many posts per page
```

12. Now you should be able to use Jekyll's pagination as normal and complete your site

13. To deploy this site is a little bit less straightforward since GitHub will automatically run the files in your master branch through Jekyll when you push. GitHub won't, however, let you run custom plugins. The solution is to use Jekyll's local output and bypass GitHub's use of it. There are many ways to do this but personally I think the easiest is to move your site's source to a non-standard location and then use a pre-commit hook to copy the files into your root directory, add the new ones and then commit/push. This sounds more complicated than it is.

```bash
$ mkdir _source
$ mv _includes _layouts _posts css js img robots.txt 404.html index.html blog.html _source
$ touch ./git/hooks/pre-commit
$ echo '#!/bin/sh' >> ./git/hooks/pre-commit
$ echo 'cp -rf ./_site/* ./' >> ./git/hooks/pre-commit
$ echo 'git add .' >> ./git/hooks/pre-commit
$ chmod +x ./git/hooks/pre-commit
$ echo source: ./_source >> _config.yml
$ echo plugins: ./_source/_plugins >> _config.yml
```

All we've done is moved our source (except for our `_config.yml`) into a directory called `_source` and updated our config file to our new location. Jekyll can still be run from the root directory and the site should build to the `_site` directory and function as normal. In order to simplify deployment we add a `pre-commit` script to our git hooks directory and tell it copy the files from `_site` to our root dir. Since `pre-commit` gets run before the commit, we need to explicitly add our new files via `git add` so they're seen by git before the commit is actually made.

GitHub's Jekyll will ignore your `_source` directory as it starts with an underscore so no processing will take place. If you prefer, you can add an empty file named `.nojekyll` to your repo's root directory to turn Jekyll off completely.  

14. To finalize our deployment we simply push our repo to GitHub

```bash
$ git push origin master
```

The site will now (or shortly) be available at <yourgithubusername>.github.com. You can find more information [on using Jekyll here](https://github.com/mojombo/jekyll/wiki). 

15. If you want to use a custom domain it's possible to do so. [You can read about it here.](https://help.github.com/articles/setting-up-a-custom-domain-with-pages)

