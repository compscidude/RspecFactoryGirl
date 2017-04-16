# RspecFactoryGirl

### Setup & Testing Rails Application with Rspec and Factory Girl

## Guidelines

1. Installation
2. Factory Girl 
    * Models
    * Traits
    * Sequence
    * Callbacks
3. Test API end-points with Rspec (Controllers)
4. Test Models


## 1. Installation

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

## 2. Factory Girl

spec/factories/users.rb
```ruby
FactoryGirl.define do
  factory :user do
    name "Michael Pearson"
    age 20
    email "Kevin@gmail.com"
    height 185
  end
end
```
Create factory user in *_spec.rb
```ruby
user = create(:user)

# or you can pass in attributes to override 
user2 = create(:user, name: "John Doe", age: 30)
user3 = create(:user, name: "Larry King" age: 23)
```
