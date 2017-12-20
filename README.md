# Flarum documentation

Hi! Looking to contribute documentation to [Flarum](http://flarum.org)? That is great, we can use all the help. Flarum is evolving rapidly and so should its documentation.

_This repo will eventually be deprecated in favour of [flarum/docs](https://github.com/flarum/docs). But until a definitive conversion date is planned, you are welcome to contribute here._

# Get started 

You can edit small changes online using the Github editor. For larger edits it is recommended to make a local checkout and test your changes before you commit them. To do that, you need Ruby. Instructions for Linux/Mac:

```
git clone https://github.com/flarum/flarum.github.io
cd flarum.github.io

cat > Gemfile <<EOM
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
EOM

bundle install
bundle exec jekyll serve
```

Now, you can view your changes through http://localhost:4000. Jekyll will automatically rebuild the site when you save your edits.
