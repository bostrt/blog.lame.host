# blog.lame.host

# Deploy

```
oc new-app --name=blog -e HUGO_BASE_URL=blog.lame.host -e HUGO_THEME=simple-hugo-theme mfojtik/hugo~https://github.com/bostrt/blog.lame.host.git
```
