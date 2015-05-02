Title: Pelican + Travis + Github Pages
Date: 2015-05-02
Category: tutorial
Tags: tutorial, python, blog, pelican, github

Hello, this is my first blob post!! I'm here to guide you guys on setting up a blog  with [Pelican](http://blog.getpelican.com/) and [Travis CI tool](https://travis-ci.org/) hosted by [Github Pages](https://pages.github.com/).

Pelican is a static site generator written in Python that translate Markdown and rST to HTML, it supports customized themes, RSS feeds, [DISQUS](https://disqus.com/) for comments and much more.
Github pages is a free hoster for projects and personal pages, that is going to host our personal blog.
Finally, Travis is a tool for automated testing and deploy, so that any commit to the blog repo will trigger a deploy.

I decided to begin the blog with this tutorial because I couldn't find any post for setting up a personal github page with pelican and travis. Let the code begins!

### Setup github stuff

Start by creating a profile and a project to store the source of your blog on [github.com](https://github.com). Create a project with the name `<profile_name>.github.io` and clone into your computer:
```
$ git clone https://github.com/<profile_name>/<profile_name>.github.io.git
```
When you create a repo with the name `<profile_name>.github.io`, Github Pages publish the whole content on `master` branch to this page `http://<profile_name>.github.io/`, for example, mine is in [http://lucasbrunialti.github.io/](http://lucasbrunialti.github.io/). Because of that, we're going to do a hack, putting pelican source to a branch called `src` and generate the page source (html, css and stuff) to `master`, so that, create and checkout to a branch called `src`:
```
$ git checkout -b src
```

### Starting with pelican

As pelican is a python project, it requires the installation of pip (Python Package Index) and virtualenv (this is not really a dependency, but it is good to keep dependencies of python projects organized):
```
$ sudo easy_install pip
$ pip install virtualenv
$ pip install pelican markdown
```

Setup a virtualenv for your pelican:
```
$ virtualenv ~/virtualenvs/pelican
$ source ~/virtualenvs/pelican/bin/activate
```

Now, install pelican:
```
$ pip install pelican
```

Now it is possible to list the dependencies of your blog:
```
$ pip freeze > requirements.txt
```

Auto generate the source with:
```
$ pelican-quickstart
```

It is going to ask a bunch of questions, the answers were given for this tutorial, but you can answer it on your own:
```
> Where do you want to create your new web site? [.]
> What will be the title of this web site? <profile_name>
> Who will be the author of this web site? <name>
> What will be the default language of this web site? [en]
> Do you want to specify a URL prefix? n
> Do you want to enable article pagination? (y/n) y
> How many articles per page do you want? [10]
> Do you want to generate a Fabfile/Makefile to automate generation and publishing? (y/n) y
> Do you want an auto-reload & simpleHTTP script to assist with theme and site development? (y/n) n
> Do you want to upload your website using FTP? (y/n) n
> Do you want to upload your website using SSH? (y/n) n
> Do you want to upload your website using Dropbox? (y/n) n
> Do you want to upload your website using S3? (y/n) n
> Do you want to upload your website using Rackspace Cloud Files? (y/n) n
> Do you want to upload your website using GitHub Pages? (y/n) y
> Is this your personal page (username.github.io)? (y/n) y
```

Check out the project's structure now:
```
$ find .
./content
./fabfile.py
./LICENSE
./Makefile
./output
./pelicanconf.py
./publishconf.py
./requirements.txt
```

It is possible to edit config in the `pelicanconf.py` file.

Now make a `hello world` page in `content/hello_world.md`:
```
Title: Hello World Pelican
Date: 2015-05-02 15:50
Category: hello world
Tags: hello world, pelican, markdown
Author: <your_name>

Hello World with Pelican!
```

Make the html source and start a server, this is going to use the [Make tool](http://www.gnu.org/software/make/) to generate html source to `./output` and start a simple server ([python SimpleHTTPServer](https://docs.python.org/2/library/simplehttpserver.html)).
```
$ make html && make serve
```

Now, check your blog in your browser at `localhost:8000/`.

To publish your blog and make it all automated, start by pushing the source to github:
```
$ git add -A
$ git commit -m "Initial commit"
$ git push origin src
```

### Setup Travis

Travis is going to checkout the code and push the output of `make html` to your `master` branch. Go to [travis-ci.org](https://travis-ci.org/) and make an account.
Link it with your github account, sync travis to fetch your github repositories and flick the repo `<profile_name>/<profile_name>.github.io` switch on.

Now, for this to work, add a file `.travis.yml` to your repo, with the following content:
```
language: python
branches:
  only:
  - src
install:
- pip install -r requirements.txt
script:
- make github
```

Also, to do the trick of publishing the `output` folder in `src` branch to `master` branch, you need to edit the `github` step in the `Makefile`:
```
...
github: publish
        ghp-import -m "Generate Pelican site" -b $(GITHUB_PAGES_BRANCH) $(OUTPUTDIR)
        @git push -fq https://${GH_TOKEN}@github.com/$(TRAVIS_REPO_SLUG).git $(GITHUB_PAGES_BRANCH) > /dev/null
...
```

Since travis will push to your repo, it has to authenticate on Github, and you can set it up by generating an [OAuth Token](https://developer.github.com/v3/oauth/) on [Github settings](https://github.com/settings/tokens). Click on `Generate new token` and give the `repo` permission.
To keep it secret, use the travis app to encrypt it and add the encrypted token to your `.travis.yml`:
```
$ brew install ruby # if you don't have ruby installed
$ gem install travis -v 1.7.6 --no-rdoc --no-ri # install travis command line
$ travis encrypt GH_TOKEN=<generated_token> --add
```

Finally, push the source and watch the magic at `https://travis-ci.org/<profile_name>/<profile_name>.github.io`. If you got everything right, your blog will be at `http://<profile_name>.github.io/`.
```
$ git add Makefile && git commit -m "trick to publish html (src -> master)"
$ git add .travis.yml && git commit -m "travis setup"
$ git push origin src
```

That's it folks! Stay tuned for more cs, programming, hacking and data science content!

