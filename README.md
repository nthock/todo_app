# Todo List App

This application is the demonstration of the first session in Singapore Rails Learning Group. It uses SQLite as the database.

## Steps to develop this application

### Step 1: Update your gemfile to include the testing suite

Update your gemfile to include the below gems:

```ruby
group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platform: :mri
  gem 'rspec-rails'
  gem 'factory_girl_rails'
end

group :test do
  gem 'rails-controller-testing'
  gem 'faker'
  gem 'capybara'
  gem 'database_cleaner'
  gem 'launchy'
  gem 'selenium-webdriver'
end
```

Then run the following command to install rspec:

```
rails generate rspec:install
```

This will add the following files:

* `.rspec`
* `spec/spec_helper.rb`
* `spec/rails_helper.rb`

Use the `rspec` command to run your specs:

```
bundle exec rspec
```

You should see `0 examples, 0 failures` in your terminal/bash.

### Step 2: Generate Rails Scaffold (Optional)

This step is optional. The idea is to show you what Rails actually help you to do by default. As this is more of a demonstration and not the actual coding of the application, we will not use the actual naming convention.

Generating scaffold is the fastest way to develop an application. A scaffold includes a controller (with the default 7 controller actions), model, and views.

This is a Todo list application. So the user will have to create a todo list, i.e. a list of things that the user want to do.

So let's assume that each todo list will need to have 2 fields:
* title: The title of the todo list (`string`)
* description: Description of the todo list (`text`)

Once you have decide on what field you want your Todo list to have, you can generate the scaffold with the following command:

```
rails generate scaffold Odot title:string description:text
```

I use Odot for the name of the todo list. It is the inverse of todo.

Migrate your database:

```
rails db:migrate
```

Migration means creating the table and the necessary columns in the database. The tables and the columns are also known as the schema.

Notice that once you run the command, you will see Rails create a list of files. They can serve as a good reference when we are building the actual todo list.

Looking at `config/routes.rb` file, you notice that there is an additional line added:

```ruby
resources :odots
```

This line added 7 default routes for the resource Odots. Try running `rails routes` in your bash/terminal, you will see the following:

```
Prefix Verb   URI Pattern               Controller#Action
    odots GET    /odots(.:format)          odots#index
          POST   /odots(.:format)          odots#create
 new_odot GET    /odots/new(.:format)      odots#new
edit_odot GET    /odots/:id/edit(.:format) odots#edit
     odot GET    /odots/:id(.:format)      odots#show
          PATCH  /odots/:id(.:format)      odots#update
          PUT    /odots/:id(.:format)      odots#update
          DELETE /odots/:id(.:format)      odots#destroy
```

For now, concentrate on the *URI Pattern* column. For example, if you see `/odots`, this means that the path is `<your root_url>/odots`. For example, if your home page is https://www.example.com, to access the `/odots`, means you have to go to https://www.example.com/odots.

In the third column *Controller#Action* tells you which controller and which action the path maps into. Let's just look at `/odots` first.

Start your rails server:

```
rails server
```

If you are using cloud9, use the following command instead:

```
rails server -b $IP -p $PORT
```

You will see a very simple page with a link to create new Odot.

![Odot Index Page](docs/img/1_Odot_index.png?raw=true "Odot Index")

Click on that link.

Then you see a form for you to fill in the title and the description. Notice that this is the fields that we have set when we generate scaffold.

![New Odot Page](docs/img/2_New_Odot.png?raw=true "New Odot Page")

Let's fill in some dummy names, then click on "Create Odot".

![Odot Created](docs/img/3_Odot_Created.png?raw=true "Odot Created")

With the Odot created, you will see the Odot that you have just created.

Click on "back", and you will go back the first page `\odots`.

![Odot Index Page](docs/img/4_Odot_Index.png?raw=true "Odot Index")

Over here, you will see a list of Odots that you have created. Let's create 2 more.

![Odot Index with Many Items](docs/img/5_Odot_Index_with_many_items.png?raw=true "Odot Index with Many Items")

Notice that you can edit the title or description by clicking "Edit" link.

![Editing Odot](docs/img/6_Editing_Odot.png?raw=true "Editing Odot")

Now you will see the `\odots` with updated title and description:

![Odot Index with Edited Items](docs/img/7_Odot_index_with_edited_item.png?raw=true "Odot Index with Edited Items")

You can also delete the todo list by clicking "Destroy".

![Odot Destroyed](docs/img/8_Odot_destroyed.png?raw=true "Odot Destroyed")

Basically that's it. Right now you have a functioning application (although it is quite ugly). Not too bad with just a single line of command, right?

### Step 3: Create Todo List without using Scaffold

Scaffold is great. But it has too much "magic" involved. There are also situations where you do not need to generate scaffold. As with most other learning programs, it is better for you to plan your application, then generate the model/controller respectively.

#### User stories

One way to plan web application is to do use "user stories". User stories are features your web application should do from the user perspectives. For the purpose of this application, I came out with the following user stories for Todo List:

1. As a user, I should be able to view all my todo lists.
2. As a user, I should be able to create todo list.
3. As a user, I should be able to archive my todo list.
4. As a user, I should be able to delete my todo list.
5. As a user, I should be able to edit the title and description of the todo list.
6. As a user, I should be able to view only my non-archive todo list.

Notice that user stories is in the form of "As a user, I should be able to ...". This make sure that the perspective is always from the user.

#### View all Todo Lists

Let's try coding the first feature. In this learning group, we are adopting the practice of test-driven development. This means that we write the test first, then we attempt to make the test pass.

You already have installed rspec from Step 1. So the first thing to do is generate a feature spec:

```
rails generate rspec:feature todo_lists
```

A file is created under `spec/features/` with the name `todo_lists_spec.rb`. Go to this file, and type the below code:

*spec/features/todo_lists_spec.rb*
```ruby
require 'rails_helper'

RSpec.feature "TodoLists", type: :feature do

  feature "view all todo lists" do

    it "should render the page successfully" do
      visit "/todo_lists"
      expect(page).to have_http_status(:success)
    end

  end

end
```

Can you guess what is this file about? It is a spec (or a test) that you have written based on a user story "I should be able to view all my todo list".

So imagine you are the user, you try to visit "/todo_lists" path, and you expect that the page to render successfully. For grouping purpose, I categorize this under the feature "view all todo lists", and the description of this spec is "should render the page successfully".

Now try running the spec:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test will fail. This is because of an error:

```
1) TodoLists view all todo lists should render the page successfully
     Failure/Error: visit "/todo_lists"

     ActionController::RoutingError:
       No route matches [GET] "/todo_lists"
```

Pay special attention to this sentence:

```
ActionController::RoutingError:
  No route matches [GET] "/todo_lists"
```

The test fail because there is no route that matches "/todo_lists".

Imagine every request is a visitor to your office. You are the boss of the company. The first thing the visitor see is your receptionist. Right now, this visitor is asking for "/todo_list". Your receptionist need to know what to do when any visitor ask for "/todo_list".

In this case, your receptionist is your route. You can tell your route (or receptionist) what to do in the `config/routes.rb` file. Insert the following line into your `routes.rb` file. It should be insert before the `end` keyword.

*config/routes.rb*
```ruby
get "/todo_lists", to: "todo_lists#index"
```

Your file should look something like this now:
```ruby
Rails.application.routes.draw do
  resources :odots
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  get "/todo_lists", to: "todo_lists#index"
end
```

This line `get "/todo_lists", to: "todo_lists#index"` tells your routes that whenever it gets a request to "/todo_lists", direct them to the controller named "todo_lists" with the "index" action. You can think of the controller "todo_lists" as a department, and the action "index" as a person in the department.

You are telling the receptionist that as long as she sees the request "/todo_lists", direct them to "index" person in the "todo_lists" department.

At this point, the receptionist job is done.

Now try running the test again.

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

You will still fail for a different reason. The error is:

```
1) TodoLists view all todo lists should render the page successfully
    Failure/Error: visit "/todo_lists"

    ActionController::RoutingError:
      uninitialized constant TodoListsController
```

Rails is telling you: "hey, you want me to direct the request to 'todo_lists' department, but there is no 'todo_lists' department! Are you kidding me?"

In a more technical term, you have not create the controller (department) yet. That's why Rails do not know what to do.

To create the department, run this command in terminal/bash:

```
rails generate controller todo_lists
```

Now your department is created. Notice that there is a new file under `app/controllers` named `todo_lists_controller.rb`. This is your department. With the department created, now run the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

Your test will still fail, yet because of a different reason:

```
1) TodoLists view all todo lists should render the page successfully
   Failure/Error: visit "/todo_lists"

   AbstractController::ActionNotFound:
     The action 'index' could not be found for TodoListsController
```

Now you have a "todo_lists" department. But you don't have "index"! Where is the person the visitor is suppose to look for?

So now you should add the action in your `todo_lists_controller.rb` like this:

*app/controllers/todo_lists_controller.rb*
```ruby
class TodoListsController < ApplicationController

  def index
  end

end
```

To add an action, you define a method with the action name as the method name. Don't forget to end the method.

Now try running the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test is still failing, of course with a different reason:

```
1) TodoLists view all todo lists should render the page successfully
     Failure/Error: visit "/todo_lists"

     ActionController::UnknownFormat:
       TodoListsController#index is missing a template for this request format and variant.

       request.formats: ["text/html"]
       request.variant: []

       NOTE! For XHR/Ajax or API requests, this action would normally respond with 204 No Content: an empty white screen. Since you're loading it in a web browser, we assume that you expected to actually render a template, not nothing, so we're showing an error to be extra-clear. If you expect 204 No Content, carry on. That's what you'll get from an XHR or API request. Give it a shot.

```

Pay attention to this line: `TodoListsController#index is missing a template for this request format and variant.`. Rails is telling you there is no template. You asked to visit a page right. But where is that page? It is missing.

So now you got to add the page, and tell your "index" controller to render that page.

By default `todo_lists` controller will look for a file under the `views/todo_lists` folder.

To add the page, you can create a file call `index.html.erb` under the folder `views/todo_lists` manually, or run the following command in terminal/bash:

```
touch app/views/todo_lists/index.html.erb
```

Now you have an empty page. Then you tell your "index" action to render that page.

*app/controllers/todo_lists_controller.rb*
```ruby
class TodoListsController < ApplicationController

  def index
    render 'index'
  end

end
```

Now try running the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

And the test finally passed! It is not too difficult right?

One more thing:

Rails automatically render the page with the same name as the controller action. This means that if your controller action is "index", it will render "index.html.erb". If the controller action is "show", it will automatically render "show.html.erb".

Therefore, there isn't really a need to call `render "index"` in the `index` controller action. Remove it

*app/controllers/todo_lists_controller.rb*
```ruby
class TodoListsController < ApplicationController

  def index
  end

end
```

and then run the specs again.

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test should still pass.

Now, the page has nothing. It is pretty boring. Let's add a test saying that there visitor should expect to see the content "Your Todo Lists".

*spec/features/todo_lists_spec.rb*
```ruby
require 'rails_helper'

RSpec.feature "TodoLists", type: :feature do

  feature "view all todo lists" do

    it "should render the page successfully" do
      visit "/todo_lists"
      expect(page).to have_http_status(:success)
      expect(page).to have_text("Your Todo Lists")
    end

  end

end
```

Run the test.

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test should fail now.

```
1) TodoLists view all todo lists should render the page successfully
     Failure/Error: expect(page).to have_text("Your Todo Lists")
       expected to find text "Your Todo Lists" in ""
     # ./spec/features/todo_lists_spec.rb:10:in `block (3 levels) in <top (required)>'

```

Go to `app/views/todo_lists/index.html.erb`, and enter the following:

*app/views/todo_lists/index.html.erb*
```html
<h2>Your Todo Lists</h2>
```

Run the test again, and now it passes.

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

Right now, this page looks something like this:

![Empty Todo List](docs/img/9_Blank_todo_list.png?raw=true "Blank Todo List")

Still pretty boring. Up until now, you are only generate static web pages. What about the actual todo list?

Now is the time to do it.

In your specs file, amend the code to the below:

*spec/features/todo_lists_spec.rb*
```ruby
require 'rails_helper'

RSpec.feature "TodoLists", type: :feature do

  feature "view all todo lists" do

    before :each do
      @todo_list = FactoryGirl.create(:todo_list)
    end

    it "should render the page successfully" do
      visit "/todo_lists"
      expect(page).to have_http_status(:success)
      expect(page).to have_text("Your Todo Lists")
      expect(page).to have_text(@todo_list.title)
    end

  end

end
```

This line of code is to test that the first todo list is in this page. Running this spec file will result in failing test. The error message is:

```
Failures:

  1) TodoLists view all todo lists should render the page successfully
     Failure/Error: @todo_list = FactoryGirl.create(:todo_list)

     ArgumentError:
       Factory not registered: todo_list

```
The error is that there is no Factory registered for `todo_list`. A factory (from `factory_girls_rails` gem) is like a factory that creates records in the test database. A factory file will be created when you create the model.

If follow Step 2, you should have seen the `spec/factories/odots.rb` file. You will define a blueprint of your record in that file. Then call the `FactoryGirl.create` method to create the record in the test database.

While we can manually create the factory file in order to pass the test, but we are not going to do that. This is because we are going to generate the `todo_list` model soon, and a factory file will be automatically generated.

##### Going back to the drawing board

Now that we need to create the model before we can move on, let's take a step back and understand what kind of data we will include in todo_list table. Recall that the user stories are:

1. As a user, I should be able to view all my todo lists.
2. As a user, I should be able to create todo list.
3. As a user, I should be able to archive my todo list.
4. As a user, I should be able to delete my todo list.
5. As a user, I should be able to edit the title and description of the todo list.
6. As a user, I should be able to view only my non-archive todo list.

This means that at minimal, there should be a title and description field, similar to Odot. As the user is able to archive the todo_list, I also need another field archive, which is a boolean.

When defining the table structure, you also need to consider for each of the field:
* Whether can there be an empty value or it must be present.
* What's the default value if there is nothing entered.
* The format for decimal places if the field contains decimals.
* Whether should you index this field (more on this in future).

So the table structure for todo_list will be:
* title: Title of the todo list (`string`). It must be present.
* description: Description of the todo list (`text`)
* archive: Whether is the todo list archive (`boolean`). The default is `false`

So run the following command to generate the model:

```
rails generate model Todo_list title:string description:text archive:boolean
```

Running this command generate the following file:

```
Running via Spring preloader in process 7306
      invoke  active_record
      create    db/migrate/20170822072148_create_todo_lists.rb
      create    app/models/todo_list.rb
      invoke    rspec
      create      spec/models/todo_list_spec.rb
      invoke      factory_girl
      create        spec/factories/todo_lists.rb
```

The first file is the migration file. Let's take a look at it. Note that the numbers are timestamped. You will see a different number when attempting this tutorial:

*db/migrate/20170822072148_create_todo_lists.rb*
```ruby
class CreateTodoLists < ActiveRecord::Migration[5.0]
  def change
    create_table :todo_lists do |t|
      t.string :title
      t.text :description
      t.boolean :archive

      t.timestamps
    end
  end
end
```

In this migration file, you are telling Rails to create a table in the database call "todo_lists", with the column "title", "description", and "archive", and its respective data types.

By default, Rails will also include a timestamps to record the time when an entry to the table is created or updated via `t.timestamps`.

Go back to our data structure again:
* title: Title of the todo list (`string`). It must be present.
* description: Description of the todo list (`text`)
* archive: Whether is the todo list archive (`boolean`). The default is `false`

You can define this within your migration file. Change your migration file to the following:

*db/migrate/20170822072148_create_todo_lists.rb*
```ruby
class CreateTodoLists < ActiveRecord::Migration[5.0]
  def change
    create_table :todo_lists do |t|
      t.string :title, null: false
      t.text :description
      t.boolean :archive, default: false

      t.timestamps
    end
  end
end
```

Then migrate the database:

```
rails db:migrate
rails db:migrate RAILS_ENV=test
```

The table is now created in the database.

The next step is to define the factory file. Change the code to the following:

*spec/factories/todo_lists.rb*
```ruby
FactoryGirl.define do
  factory :todo_list do
    title "Sample todo list"
    description "This is a sample todo list"
    archive false
  end
end
```

You define a default record to create. In this case, the title will be "Sample todo list" and the description will be "This is a sample todo list". Running `FactoryGirl.create(:todo_list)` will create a record with these information in the test database.

With the Factory file created, you can try running the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test will fail. Because it cannot find the text "Sample todo list" in the `app/views/todo_lists/index.html.erb`.

```
Failures:

  1) TodoLists view all todo lists should render the page successfully
     Failure/Error: expect(page).to have_text(@todo_list.title)
       expected to find text "Sample todo list" in "Your Todo Lists"
     # ./spec/features/todo_lists_spec.rb:15:in `block (3 levels) in <top (required)>'
```

In a strict test-driven development, you write the simplest code to let the code pass. In this case, you should be tempted to just write the text "Sample todo list" to pass the test. It's all alright, let's do that:

*app/views/todo_lists/index.html.erb*
```html
<h2>Your Todo Lists</h2>
<p>
  Sample todo list
</p>
```

Running the test again, now your test passed.

But you know this is not right. But how come your test passed? This is because your test is not written correctly. Let's change your test:

*spec/features/todo_lists_spec.rb*
```ruby
require 'rails_helper'

RSpec.feature "TodoLists", type: :feature do

  feature "view all todo lists" do

    before :each do
      @todo_list_1 = FactoryGirl.create(:todo_list, title: "Personal learning", description: "List of things I have to do for personal learning")
      @todo_list_2 = FactoryGirl.create(:todo_list, title: "Work", description: "Work list items")
      @todo_list_3 = FactoryGirl.create(:todo_list, title: "Home", description: "List of home items to do")
    end

    it "should render the page successfully with all the todo list items" do
      visit "/todo_lists"
      expect(page).to have_http_status(:success)
      expect(page).to have_text("Your Todo Lists")
      todo_lists = TodoList.all
      todo_lists.each do |todo_list|
        expect(page).to have_text(todo_list.title)
      end
    end

  end

end
```

In this updated test, I changed 3 things:
* First, I create 3 todo lists with the title "Personal Learning", "Work", and "Home"
* Second, call for all the todo lists created using `TodoList.all` and save it in the variable `todo_lists`. Then I loop through `todo_lists` and expect that the page should have each of the title.
* Third, I change the specs description to reflect the nature of this specs.

Running the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

It will result in a failure:

```
1) TodoLists view all todo lists should render the page successfully
   Failure/Error: expect(page).to have_text(todo_list.title)
     expected to find text "Personal learning" in "Your Todo Lists Sample todo list"
   # ./spec/features/todo_lists_spec.rb:19:in `block (4 levels) in <top (required)>'
   # ./spec/features/todo_lists_spec.rb:18:in `block (3 levels) in <top (required)>'
```

In this case, I have to make the page show all the todo list items.

To fix this, we will have to do 2 things:
1. Extract all the TodoList items in database, and save it as an instance variable in the `index` action of the controller.
2. Loop through this instance variable in the `index.html.erb` and display it as a text.

To extract all the TodoList items in the controller, write the following code:

*app/controllers/todo_lists_controller.rb*
```ruby
class TodoListsController < ApplicationController

  def index
    @todo_lists = TodoList.all
  end

end
```

With the instance variable `@todolists` declared, now the view `index.html.erb` will have access to this instance variable.

*app/views/todo_lists/index.html.erb*
```html
<h2>Your Todo Lists</h2>

<% @todo_lists.each do |todo_list| %>
  <p><%= todo_list.title %></p>
<% end %>
```

You can enter ruby code within the `<%` and `%>`.

This line:

```
<% @todo_lists.each do |todo_list| %>
```

Loop through the instance variable `@todo_lists` and save each element in the variable `todo_list`.

```
<p><%= todo_list.title %></p>
```

Then we return the title of `todo_list` in each loop in html with `<%=` and `%>`.

Let's run the test again:

```
bundle exec rspec spec/features/todo_lists_spec.rb
```

The test is now passed.

##### Generating Dummy Data

Now you do not see anything in your screen except for the title because there is no data in the development database. You can generate dummy data with 2 ways:
* Go to Rails console and create the data
* Using Rails db:seed

In this section, I will illustrate using Rails db:seed. Enter the following code:

*db/seeds.rb*
```ruby
TodoList.create(title: "Personal Learning", description: "List of things I have to do for personal learning")
TodoList.create(title: "Work", description: "Work list items")
TodoList.create(title: "Home", description: "List of home items to do")
```

Then run the below command to generate the dummy records:

```
rails db:seed
```

Go back to your index page again, now you will see the title of the todo list in it.

With that, we have finally completed the first user story: **As a user, I should be able to view all my todo lists.**

![Populated Todo List](docs/img/10_Populated_todo_list.png?raw=true "Populated Todo List")

#### Create Todo List

Now you can view all your created todo lists, but how do you create them in the first place?

In this section, I will show you how to do it - specifically, the user story:

**As a user, I should be able to create todo list.**

As usual, let's write the behavior in rspec first.

#### Todo List Validations

*spec/features/todo_lists_spec.rb*
```ruby
require 'rails_helper'

RSpec.feature "TodoLists", type: :feature do

  feature "view all todo lists" do
    # Hidden so that this code example is shorter. It will be the same as the above.
  end

  feature "create a todo list" do

    it "should create one todo list" do
      visit "/todo_lists/new"
      expect(page).to have_http_status(:success)
      expect(page).to have_text("Create New Todo List")
      fill_in "title", with: "School Work"
      fill_in "description", with: "List of projects / readings to do"
      click_button "Create Todo List"

      expect(current_path).to eq "/todo_lists"
      expect(page).to have_text("School Work")
      expect(TodoList.find_by(title: "School Work").where).to eq 1
    end
  end

end
```

When you run this test, your test will fail. The first issue is routing issues, let's fix that:

*config/routes.rb*
```ruby
Rails.application.routes.draw do
  resources :odots
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  get "/todo_lists", to: "todo_lists#index"
  get "/todo_lists/new", to: "todo_lists#new"
end
```

Then, rspec complains that there is no "new" action for the controller. But before we fix that, let's do a little refactoring.

You notice that the 2 routes we define: `get "/todo_lists, to: "todo_lists#index"` and `get "/todo_lists/new", to: "todo_lists#new"` are somewhat similar. If we run Rails routes, we will get something like this:

```
Prefix Verb   URI Pattern               Controller#Action
todo_lists GET    /todo_lists(.:format)     todo_lists#index
todo_lists_new GET    /todo_lists/new(.:format) todo_lists#new
```

Turns out that Rails has a function for such routes. These routes are known as the standard routes comprising of 7 actions: index, new, create, edit, update, destroy and show.

To get these routes, we can simply use the following code:

```ruby
resources :todo_lists
```

So change your route file to use this instead of the 2 separate routes:

*config/routes.rb*
```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  resources :todo_lists
end
```

Running `rails routes` again, you will get the following:

```
Prefix Verb      URI Pattern                           Controller#Action
todo_lists       GET    /todo_lists(.:format)          todo_lists#index
                 POST   /todo_lists(.:format)          todo_lists#create
new_todo_list    GET    /todo_lists/new(.:format)      todo_lists#new
edit_todo_list   GET    /todo_lists/:id/edit(.:format) todo_lists#edit
todo_list        GET    /todo_lists/:id(.:format)      todo_lists#show
                 PATCH  /todo_lists/:id(.:format)      todo_lists#update
                 PUT    /todo_lists/:id(.:format)      todo_lists#update
                 DELETE /todo_lists/:id(.:format)      todo_lists#destroy
```
One line of code, you get 7 routes.

But you have an issue. You don't need these 7 routes for now. You only need the `index` and `new`. In this case, you can specify what you want using the following code:

```ruby
resources :todo_lists, only: [:index, :new]
```

Let's change this:

*config/routes.rb*
```ruby
Rails.application.routes.draw do
  resources :odots
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  resources :todo_lists, only: [:index, :new]
end
```

When you run `rails routes` again, you should only see the below 2 routes:

```
Prefix Verb      URI Pattern                           Controller#Action
todo_lists       GET    /todo_lists(.:format)          todo_lists#index
new_todo_list    GET    /todo_lists/new(.:format)      todo_lists#new
```

Now run your test again to make sure that the same error message pops up.

So we will define the `new` action in the controller.

*app/controllers/todo_lists_controller.rb*
```ruby
class TodoListsController < ApplicationController

  def index
    @todo_lists = TodoList.all
  end

  def new
    @todo_list = TodoList.new
  end

end
```

Over here, you define an instance variable `@todo_list` and call the `TodoList` model class, `new` method. Think of this
