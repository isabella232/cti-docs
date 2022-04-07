# CTI Docs

--> https://elastic.github.io/cti-docs/ <--

## About

CTI docs is intended as a developer onboarding resource for the Detections & Response CTI team. It can also be useful for designers, testers, product managers, SDH engineers, and documentation writers. CTI docs is divided into a few sections (Backend, Frontend, Development Setup, Performance, Manual Regression Suites), and a left-hand sidebar includes links to headers for easy navigation.

## Maintenance

To update the CTI docs, please use the following steps:

1. clone this repo
2. run `bundle install`
3. add markdown files in the `_documents` directory
4. update `_layouts/default.html` to update the sidebar links
5. delete `docs` and `_site` directories if they exist   
5. run `bundle exec jekyll build` to build
6. run `bundle exec jekyll serve` to serve
7. once you are ready, rename the `_site` directory to `docs`, and push to `main`
8. https://elastic.github.io/cti-docs/ will be automatically updated in a few minutes

## Reuse the documentation template for another project

1. clone this repo and update the remote tracking branch to your own empty repo
2. run `bundle install`
3. delete all files in the `_documents` directory and add some of your own  
4. Update the project title and the sidebar links in the `_layouts/default.html`
5. delete `docs` and `_site` directories if they exist
6. run `bundle exec jekyll build` to build
7. run `bundle exec jekyll serve` to serve
8. once you are ready, rename the `_site` directory to `docs`, and push to `main`
9. go to the Pages section of your github project settings, and choose `docs` from the blog source dropdown
10. you can find the link to your blog on the same github settings page
