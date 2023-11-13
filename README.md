# Sebastian's Website

Testing Hugo with Clarity theme to create a (GitHub) Pages hosted website.

Let's see how much faff it is to configure and write posts and how much work it is again to get my Markdown files rendered properly.


## Getting Started

How I set this site up from the Hugo Clarity theme example site, configured it and set up GitHub pages to build and deploy it.

1. fork Hugo theme repository of choice, I liked [Clarity](https://github.com/chipzoller/hugo-clarity)
1. clone for local editing and start local server
   ```bash
   git clone https://github.com/chipzoller/hugo-clarity
   cd hugo-clarity/exampleSite
   hugo server --themesDir ../.. # instruct Hugo to use this repo as the theme
   ```
1. configure: locate and edit config files (TOML file type for Hugo), e.g. `nvim $(find . -name *.toml)`
1. (re)move example posts in `exampleSite/content/post`
1. Find and edit About page etc. There are a lot of files, but these should be Markdown files, e.g. `about.md`.
1. (optional) rename website directory, in this case (also change files pointing to old dir.)
   ```bash
   git mv exampleSite <website directory>
   nvim $(grep -rl exampleSite) # find and edit files pointing to old dir.
   ```
1. Commit changes, add URL to personal repo and push
   ```bash
   git add .
   git commit -m <commit message>
   git remote rename upstream
   git remote add origin <personal repo URL, e.g. on github.com>
   git push -u origin <branch>
   # I chose production as the theme was using master, just configure this branch in Pages settings
   ```
1. set up Pages and build + deploy (GitHub Pages, use similar config for build+deploy on e.g. GitLab)
    1. activate Pages in project settings
    1. choose `GitHub actions` under build and deployment, make sure to use the correct branch
    1. search actions for Hugo, I got exactly one result. This creates a file `.github/workflows/hugo.yml` with the required settings and scripts. In my case I had to make minor adjustments (as I hadn't set up the Hugo site with `hugo init` and put the theme in the `themes/` directory):
       ```yaml
       - name: Build with Hugo
         env:
           # For maximum backward compatibility with Hugo modules
           HUGO_ENVIRONMENT: production
           HUGO_ENV: production
         run: |
           # theme isn't separate from site - build in subdir, look for theme in parent dir, move
           # built pages to root dir for deployment
           cd <website directory>
           hugo \
             --themesDir ../.. \
             --minify \
             --baseURL "${{ steps.pages.outputs.base_url }}/"
           cd "$OLDPWD"
       - name: Upload artifact
         uses: actions/upload-pages-artifact@v2
         with:
           path: ./website/public
       ```
