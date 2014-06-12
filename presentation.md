class: center, middle

# Automated Testing Story
## Kuba Kuźma

Silesian Ruby Users Group

Friday, June 13, 2014

---

class: center, middle

# Let's get back to 2007

---

class: center, middle

# Rails has a **test** folder

---

class: center, middle

# RSpec aka BDD

---

# RSpec aka BDD

```ruby
describe Game do
  before(:each) do
    @game = Game.new
  end

  it "should score 0 for gutter game" do
    1.upto(20) { @game.hit(0) }
    @game.score.should == 0
  end
end
```

---

# RSpec

* use Ruby superpower - DSLs
* well organized tests - contexts
* DRY tests
* readable tests
* avoid mistakes with writing `assert 1, a` instead of `assert_equal 1, a`
* write *specs*, not *tests*

---

# RSpec Reality

```ruby
describe Order do
  # (…) 200 lines of code, 6 nested contexts
              context "deliver" do
                it "should change state respectively"
                  # DSL FTW!
                  order.should not_be_pending
                  order.items.count == 1
                end
              end
  # 200 more lines (…)
end
```

* very deeply nested contexts
* DSL confusion
* `shared_example`, `behave_like`, `expect`, `let`, `let!`, `subject`

---

# RSpec Legacy

Let's make tests **blazing fast**!

```ruby
Order.stub(:find).and_return(…)
Order.any_instance.stub(:items).and_return([…])
```

---

class: center, middle

# Cucumber aka SDD

---

# Cucumber aka SDD

```cucumber
Scenario: Articles List
  Given I have articles titled Pizza, Breadsticks
  When I go to the list of articles
  Then I should see "Pizza"
  And I should see "Breadsticks"
```

Documentation that client can easily read and understand

---

# Cucumber Reality (feat. Pickle)

```cucumber
Given a user exists
And another user exists with role: "admin"
# later
Then a user should exist with name: "Fred"
And that user should be activated # rspec matchers
Scenario: Articles List
```

* capybara fine grained steps (`click_on`, `fill_in`, …)
* bunch of step files filled with regular expressions (`git grep` anyone?)

---

# Other tools, part 1

* **Shoulda**
* **Spinach** - Spinach is a BDD framework on top of Gherkin
* **Steak** - delicious combination of RSpec and Capybara for Acceptance BDD
* **BBQ** - Object oriented acceptance testing
* **Coulda** - Behaviour Driven Development derived from Cucumber but as an internal DSL with methods for reuse
* **Filet** - Extension for Test::Unit to have a Steak-like DSL for acceptance testing
* **Bacon** - Bacon is a small RSpec clone weighing less than 350 LoC

---

# Other tools, part 2

* **Wrong** - Wrong provides a general assert method that takes a predicate block
* **Riot** - An extremely fast, expressive, and context-driven unit-testing framework. A replacement for all other testing frameworks
* **Shindo** - Work with your tests, not against them
* **mustard** - Simple "must" expectations for tests and specs in Ruby.
* **Minitest** - minitest provides a complete suite of testing facilities supporting TDD, BDD, mocking, and benchmarking

many, many more…

---

class: center, middle

# TDD is dead

---

# Test::Unit

* almost the same syntax as 10 years ago
* classical object oriented approach - classes and inheritance

```ruby
class UserTest < ActiveSupport::TestCase
  include ActiveModel::Lint::Tests
end
```

* it is already in your bundle

---

# Factory Girl

* syntax has significantly changed over the past few years
* include `FactoryGirl::SyntaxMethods`

```ruby
FactoryGirl.define do
  factory :booking do
    association :restaurant
    association :client, :active
    number_of_people 1
    booking_at { 4.hours.from_now }

    trait :paid do
      paid_at { DateTime.now }
      paid_amount "20.00"
    end
  end
end
```

---

# BBQ approach

* make tests more readable
* test interactions between users

```ruby
test "receives an email with the reservation request" do
  agent = TestUser::Gatekeeper.new
  gatekeeper.sign_in
  gatekeeper.go_on_duty

  assert_difference("gatekeeper.deliveries.size") do
    client = TestUser::Client.new
    client.sign_in
    client.request_booking(gatekeeper.restaurant.name)
    assert client.see?("Thank you")
  end
end
```

---

# VCR and webmock

* network traffic stubbing
* easy to switch off and to test live API

```ruby
test "returns true if masspay succeedes" do
  use_cassette(__method__) do
    booking = create(:booking, :rated)
    assert PayoutService.new(booking).call
    assert booking.paid?
    assert_equal 10.0, booking.paid_amount
  end
end
```

---

# Apipie

* autogenerate and organize examples from your controller tests
* params validation

```ruby
resource_description do
  resource_id "sessions"
end

api :POST, "/v1/session", "Returns an auth token"
param :session, Hash, required: true do
  param :email, String, desc: "Email", required: true
  param :password, String, desc: "Password", required: true
end
```

---

background-image: url(images/apipie.png)

---

class: center, middle

# Questions?
