## RB120 - Object Oriented Programming --> OO Easy 1

### Exercise 1: Banner Class

Behold this incomplete class for constructing boxed banners.

```ruby
class Banner
  def initialize(message)
  end

  def to_s
    [horizontal_rule, empty_line, message_line, empty_line, horizontal_rule].join("\n")
  end

  private

  def horizontal_rule
  end

  def empty_line
  end

  def message_line
    "| #{@message} |"
  end
end
```

Complete this class so that the test cases shown below work as intended. You are free to add any methods or instance variables you need. However, do not make the implementation details public.

You may assume that the input will always fit in your terminal window.

Test Cases:
```ruby
banner = Banner.new('To boldly go where no one has gone before.')
puts banner
+--------------------------------------------+
|                                            |
| To boldly go where no one has gone before. |
|                                            |
+--------------------------------------------+
```

```ruby
banner = Banner.new('')
puts banner
+--+
|  |
|  |
|  |
+--+
```

#### Solution

```ruby
class Banner
  def initialize(message, width)
    @message = message
    @width = width

    if @width < message.size
      @width = message.size
    elsif @width > 55
      @width = 55
    else
      @message = message
      @width = width
    end
  end

  def to_s
    [horizontal_rule, empty_line, message_line, empty_line, horizontal_rule].join("\n")
  end

  private

  def empty_line
   "| #{' ' * @width} |"
  end

  def horizontal_rule
   "+-#{'-' * @width}-+"
  end

  def message_line
    "| #{@message.center(@width)} |"
  end
end

banner = Banner.new('To boldly go where no one has gone before.', 48)
puts banner
```

#### Further exploration

Modify the class so `new` accepts an specific banner width at the time the banner is created.

```ruby
class Banner
  def initialize(message, width)
    @message = message
    @width = width

    if @width < message.size
      @width = message.size
    elsif @width > 55
      @width = 55
    else
      @message = message
      @width = width
    end
  end

  def to_s
    [horizontal_rule, empty_line, message_line, empty_line, horizontal_rule].join("\n")
  end

  private

  def empty_line
   "| #{' ' * @width} |"
  end

  def horizontal_rule
   "+-#{'-' * @width}-+"
  end

  def message_line
    "| #{@message.center(@width)} |"
  end
end

banner = Banner.new('Hello there', 203)
puts banner

banner = Banner.new('Hello there', 20)
puts banner

banner = Banner.new('Hello there', 36)
puts banner
```

### Exercise 2: What's the Output?

Take a look at the following code:

```ruby
class Pet
  attr_reader :name

  def initialize(name)
    @name = name.to_s
  end

  def to_s
    @name.upcase!
    "My name is #{@name}."
  end
end

name = 'Fluffy'
fluffy = Pet.new(name)
puts fluffy.name
puts fluffy
puts fluffy.name
puts name
```

What output does this code print? Fix this class so that there are no surprises waiting in store for the unsuspecting developer.

#### Solution

```ruby
class Pet
  attr_reader :name

  def initialize(name)
    @name = name.to_s
  end

  def to_s
    "My name is #{@name.upcase}."
  end
end
```

### Exercise 3: Fix the Program - Books (Part 1)

Complete this program so that it produces the expected output:

```ruby
class Book
  def initialize(author, title)
    @author = author
    @title = title
  end

  def to_s
    %("#{title}", by #{author})
  end
end

book = Book.new("Neil Stephenson", "Snow Crash")
puts %(The author of "#{book.title}" is #{book.author}.)
puts %(book = #{book}.)

# Expected output
# The author of "Snow Crash" is Neil Stephenson.
# book = "Snow Crash", by Neil Stephenson.
```

#### Solution

```ruby
class Book
  attr_reader :author, :title

  def initialize(author, title)
    @author = author
    @title = title
  end

  def to_s
    %("#{title}", by #{author})
  end
end

book = Book.new("Neil Stephenson", "Snow Crash")
puts %(The author of "#{book.title}" is #{book.author}.)
puts %(book = #{book}.)
```

### Exercise 4: Fix the Program - Books (Part 2)

Complete this program so that it produces the expected output:

```ruby
class Book
  def to_s
    %("#{title}", by #{author})
  end
end

book = Book.new
book.author = "Neil Stephenson"
book.title = "Snow Crash"
puts %(The author of "#{book.title}" is #{book.author}.)
puts %(book = #{book}.)

# Expected output
# The author of "Snow Crash" is Neil Stephenson.
# book = "Snow Crash", by Neil Stephenson.
```

#### Solution

```ruby
class Book

  attr_accessor :author, :title

  def to_s
    %("#{title}", by #{author})
  end
end

book = Book.new
book.author = "Neil Stephenson"
book.title = "Snow Crash"
puts %(The author of "#{book.title}" is #{book.author}.)
puts %(book = #{book}.)
```

### Exercise 5: Fix the Program - Persons

Complete this program so that it produces the expected output:

```ruby
class Person
  def initialize(first_name, last_name)
    @first_name = first_name.capitalize
    @last_name = last_name.capitalize
  end

  def to_s
    "#{@first_name} #{@last_name}"
  end
end

person = Person.new('john', 'doe')
puts person

person.first_name = 'jane'
person.last_name = 'smith'
puts person

# Expected output
# John Doe
# Jane Smith
```

#### Solution

```ruby
class Person
  def initialize(first_name, last_name)
    @first_name = first_name.capitalize
    @last_name = last_name.capitalize
  end

  def first_name=(first_name)
    @first_name = first_name.capitalize
  end

  def last_name=(last_name)
    @last_name = last_name.capitalize
  end

  def to_s
    "#{@first_name} #{@last_name}"
  end
end
```

### Exercise 6: Fix the Program - Flight Data

Consider the following class definition:

```ruby
class Flight
  attr_accessor :database_handle

  def initialize(flight_number)
    @database_handle = Database.init
    @flight_number = flight_number
  end
end
```

There is nothing technically incorrect about this class, but the definition may lead to problems in the future. How can this class be fixed to be resistant to future problems?

#### Solution

- We may want to delete the attr_accessor, so users of this code cannot access to the data base

### Exercise 7: Fix the Program - Flight Data

Fix the following code so it works properly:

```ruby
class Car
  attr_accessor :mileage

  def initialize
    @mileage = 0
  end

  def increment_mileage(miles)
    total = mileage + miles
    mileage = total
  end

  def print_mileage
    puts mileage
  end
end

car = Car.new
car.mileage = 5000
car.increment_mileage(678)
car.print_mileage  # should print 5678
```

#### Solution

```ruby
class Car
  attr_accessor :mileage

  def initialize
    @mileage = 0
  end

  def increment_mileage(miles)
    total = mileage + miles
    @mileage = total
  end

  def print_mileage
    puts mileage
  end
end
```

### Exercise 8: Rectangles and Squares

Given the following class:

```ruby
class Rectangle
  def initialize(height, width)
    @height = height
    @width = width
  end

  def area
    @height * @width
  end
end
```

Write a class called Square that inherits from Rectangle, and is used like this:

```ruby
square = Square.new(5)
puts "area of square = #{square.area}"
```

#### Solution

```ruby
class Square < Rectangle
  def initialize(side)
    super(side, side)
  end
end
```

### Exercise 9: Complete the Program - Cats!

Update the code so that when you run it, you see the expected output:

```ruby
class Pet
  def initialize(name, age)
    @name = name
    @age = age
  end
end

class Cat < Pet
end

pudding = Cat.new('Pudding', 7, 'black and white')
butterscotch = Cat.new('Butterscotch', 10, 'tan and white')
puts pudding, butterscotch

# Expected output:
# My cat Pudding is 7 years old and has black and white fur.
# My cat Butterscotch is 10 years old and has tan and white fur.
```

#### Solution

```ruby
class Pet
  def initialize(name, age)
    @name = name
    @age = age
  end
end

class Cat < Pet
  def initialize(name, age, fur_color)
    super(name, age)
    @fur_color = fur_color
  end

  def to_s
    "My cat #{@name} is #{@age} years old and has #{@fur_color} fur"
  end
end

pudding = Cat.new('Pudding', 7, 'black and white')
butterscotch = Cat.new('Butterscotch', 10, 'tan and white')
puts pudding, butterscotch
```

### Exercise 9: Refactoring Vehicles

Considering the following classes, refactor these classes so they all use a common superclass, and inherit behavior as needed.

```ruby
class Car
  attr_reader :make, :model

  def initialize(make, model)
    @make = make
    @model = model
  end

  def wheels
    4
  end

  def to_s
    "#{make} #{model}"
  end
end

class Motorcycle
  attr_reader :make, :model

  def initialize(make, model)
    @make = make
    @model = model
  end

  def wheels
    2
  end

  def to_s
    "#{make} #{model}"
  end
end

class Truck
  attr_reader :make, :model, :payload

  def initialize(make, model, payload)
    @make = make
    @model = model
    @payload = payload
  end

  def wheels
    6
  end

  def to_s
    "#{make} #{model}"
  end
end
```

#### Solution

```ruby
class Vehicle
  attr_reader :make, :model

  def initialize(make, model)
    @make = make
    @model = model
  end

  def to_s
    "#{make} #{model}"
  end
end

class Car < Vehicle

  def wheels
    4
  end

end

class Motorcycle < Vehicle

  def wheels
    2
  end

end

class Truck < Vehicle
  attr_reader :payload

  def initialize(make, model, payload)
    super(make, model)
    @payload = payload
  end

  def wheels
    6
  end

end
```
