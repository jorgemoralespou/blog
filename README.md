= My blog
This is my personal blog

It used [hugo](https://gohugo.io/) with [zzo theme](https://github.com/zzossig/hugo-theme-zzo). [Docs for the theme](https://zzodocs.netlify.com/docs)

## Test
How to test the blog locally

```
# -D will show drafts, -F future posts
hugo server -D
```

You can then go to http://localhost:1313/

## Update the theme
From the root of your site:

```
git submodule update --remote --merge
```

## Publish
This is the process to publish the blog:

* Make the changes here
* Test hugo locally
* deploy.sh to publish the rendered blog
* Add changes to git and push


```
# Make changes

# Test them locally (-D will show drafts, -F future posts)
hugo server -D 
# Visit http://localhost:1313/
# <ctrl-c>

./deploy.sh [<message>]

# Add the changes to the blog site
git add .
git commit -m "<message>"
git push origin master
```


