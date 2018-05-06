## Development

### Blog
run `bundle exec jekyll server` that will open up the dev server at `localhost:4000`

### Landing Page

run `gulp dev` inside `landingpage` directory this will open up the dev server at `localhost:3000`


## Production

run `bundle exec jekyll build` in parent dir and then `gulp` inside landingpage

## Indexing


ALGOLIA_API_KEYALGOLIA ='your_admin_api_key' bundle exec jekyll algolia

## Upload to S3

`aws s3 cp  --recursive ./_site/ s3://meetrix.io`