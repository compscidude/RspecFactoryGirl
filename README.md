# RspecFactoryGirl

### Setup & Testing Rails Application with Rspec and Factory Girl

## Guidelines

1. Installation
2. Factory Girl 
    * Models
    * Traits
    * Sequence
    * Callbacks
    * Transient
3. Test API end points with Rspec 


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
user3 = create(:user, name: "Larry King", age: 23)
```

### Traits
Traits are grouping of attributes applied to your factory model in the creation phase.

```ruby
FactoryGirl.define do
  factory :user do
    name "Michael Pearson"
    age 20
    email "Kevin@gmail.com"
    height 185
    points 0
    
  
     trait :early_twenties do
        age rand(20..23)
     end

     trait :mid_twenties do
        age rand(24..26)
     end

     trait :late_twenties do
        age rand(27..29)
     end
     
     trait :random do
        name Faker::Name.name
        age rand(20..50)
        email Faker::Internet.email
        height rand(160..190)
     end
  end
end
```
We can now use traits using the syntax below. Note that they are not limited to just one, but the latest one will override if there 
is a duplicate attribute.

```ruby
  user = create(:user, :mid_twenties)
  user2 = create(:user, :late_twenties)
  
  # user with random attributes
  random = create(:user, :random)
```

### Sequence
Sequence is just an iterator which can be used when creating your models.

```ruby
  sequence :id do |n|
    "#{n}"
  end

   generate :id
   #=> 1

   generate :id
   #=> 2

   # This would generate users with different id for each creation
   factory :user do 
     sequence(:id) { |n| "#{n}" }
   end 
```
Each user created using create_list would be assigned a different id because of sequence used to define the id.
```ruby
   # create_list allows us to create multiple instances of the model
   users = create_list(:user, 10)
```

### Callbacks
There are four types of callbacks
   1. after(:build)
   2. before(:create)
   3. after(:create)
   4. after(:stub) 
   
```ruby
   factory :user do
     # we can chain it with the creation of something else that is associated with the user
     # eg, if users are automatically assigned a portfolio with the user_id
     after(:create) { |user| create(:portfolio, user: user) }
   end
```
Assuming that model relations are set up correctly using ActiveRecord, the following should hold.
```ruby
   user = create(:user)
   expect(user.portfolios.length).to === 1
```

### Transient
Transients are variables that are not read in by the factory model, but instead can be used to generate dynamic models if used correctly.
Let's combine everything we've learned so far.

```ruby
factory :user do
  ...
  ...
  # Child of user :: inheritance is applied
  factory :user_with_3_portfolios do   
          transient do
            portfolio_count 3
          end
          after(:create) do |user, evaluator|
            create_list(:portfolio, evaluator.portfolio_count, user: user)
          end
   end
end
```

```ruby
   user = user.create(:user_with_3_portfolios)
   # the following should hold true
   expect(user.portfolios.length).to === 3  
```

## Testing API Endpoints with Rspec

Endpoints are tested inside the *modelname_controller_spec.rb

   1. #Index
   2. #Show
   3. #Create
   4. #Update
   5. #Delete

### Accessing end points
```ruby
    before { create(:user) }
    
    get :index
    get :show, params: { id: user.id }  
    post :create, user: { name: "Kevin", age: "26" }
    patch :update, params: { id: user.id, user: { age: "23" } }
    delete :destroy, params: { id: user.id } 
```
### Techniques for testing if end points are valid
```ruby
   # code is the returned http status in the returned header (200: success, 400: bad request, 422: unprocessible entity)
   expect(response.status).to eq(code) 
   
   # expect{blockcode}.to change(..).by(count)
   expect{ post :create, user: valid_attributes}.to change(User, :count).by(1)
   
   data = JSON.parse(response.body)
   expect(data[0]["name"]).to eq("Kevin")
   
   # After creating 10 users
   expect(data.length).to eq(10)
   
   # If you are expecting an error
   expect(response.status).to eq(422)
   
   # Deleting a record
   expect{action}.to change(User, :count).by(-1)
```
