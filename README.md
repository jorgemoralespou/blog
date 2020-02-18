= My blog
This is my personal blog

== Uses
hugo with [zzo theme](https://github.com/zzossig/hugo-theme-zzo)

Docs can be found at https://zzodocs.netlify.com/docs

== Test

```
hugo server -D
```

== Update the theme
From the root of your site:

```
git submodule update --remote --merge
```

== Publish
This is the process to publish the blog:

* Make the changes here
* Test hugo locally
* deploy.sh to publish the rendered blog
* Add changes to git and push


```
# Make changes

# Test them locally
hugo server -D 
# Visit http://localhost:1313/
# <ctrl-c>

./deploy.sh [<message>]

# Add the changes to the blog site
git add .
git commit -m "<message>"
git push origin master
```


