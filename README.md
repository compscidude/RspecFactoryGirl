# RspecFactoryGirl

### Setup & Testing Rails Application with Rspec and Factory Girl

### Installing

Include the following lines in your gemfile
```ruby
  gem 'factory_girl_rails'
  gem 'rspec-rails'
```
Run the installation
```bash
  bundle install --without production
  rails g rspec:install
```
Include the following line in rails_helper.rb 
```ruby
config.include FactoryGirl::Syntax::Methods
```
## Guidelines

1. Factory Girl 
    * Models
    * Traits
    * Sequence
    * Callbacks
2. Test API end-points with Rspec (Controllers)
3. Test Models

