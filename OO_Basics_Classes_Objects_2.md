## RB120 - Object Oriented Programming --> OO Basics: Classes and Objects 2

### Exercise 1: Generic Greeting (Part 1)

Modify the following code so that Hello! I'm a cat! is printed when Cat.generic_greeting is invoked.

```ruby
class Cat
end

Cat.generic_greeting

# Expected output
# Hello! I'm a cat!
```

#### Solution

```ruby
class Cat

  def self.generic_greeting
    puts "Hello! I'm a cat!"
  end
end

Cat.generic_greeting
```

### Exercise 2: Hello, Chloe!

Using the following code, add an instance method named `#rename` that renames `kitty` when invoked.

```ruby
class Cat
  attr_accessor :name

  def initialize(name)
    @name = name
  end
end

kitty = Cat.new('Sophie')
kitty.name
kitty.rename('Chloe')
kitty.name

# Expected ouput
# Sophie
# Chloe
```

#### Solution

```ruby
class Cat
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def rename(new_name)
    self.name = new_name
  end
end

kitty = Cat.new('Sophie')
p kitty.name
kitty.rename('Chloe')
p kitty.name
```

### Exercise 3: Identify Yourself (Part 1)

Identify Yourself (Part 1)

```ruby
class Cat
  attr_accessor :name

  def initialize(name)
    @name = name
  end
end

kitty = Cat.new('Sophie')
p kitty.identify

# Expected output (object id may be different)
# #<Cat:0x007ffcea0661b8 @name="Sophie">
```

#### Solution

```ruby
class Cat
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def identify
    self
  end
end

kitty = Cat.new('Sophie')
p kitty.identify
```

### Exercise 4: Generic Greeting (Part 2)

Using the following code, add two methods: `::generic_greeting` and `#personal_greeting`. The first method should be a class method and print a greeting that's generic to the class. The second method should be an instance method and print a greeting that's custom to the object.

```ruby
class Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end
end

kitty = Cat.new('Sophie')

Cat.generic_greeting
kitty.personal_greeting

# Expected output
# Hello! I'm a cat!
# Hello! My name is Sophie!
```

#### Solution

```ruby
class Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def self.generic_greeting
    puts "Hello! I'm a cat!"
  end

  def personal_greeting
   puts "Hello! My name is #{name}!" 
  end
end

kitty = Cat.new('Sophie')

Cat.generic_greeting
kitty.personal_greeting
```

### Exercise 5: Counting Cats

Using the following code, create a class named `Cat` that tracks the number of times a new `Cat` object is instantiated. The total number of `Cat` instances should be printed when `::total` is invoked.

```ruby
kitty1 = Cat.new
kitty2 = Cat.new

Cat.total

# Expected output
# 2
```

#### Solution

```ruby
class Cat
  @@count_cats = 0

  def initialize
    @@count_cats += 1
  end

  def self.total
    puts @@count_cats
  end
end

kitty1 = Cat.new
kitty2 = Cat.new

Cat.total
```

### Exercise 6: Colorful Cat

Using the following code, create a class named Cat that prints a greeting when #greet is invoked. The greeting should include the name and color of the cat. Use a constant to define the color.

```ruby
kitty = Cat.new('Sophie')
kitty.greet

# Expected output
# Hello! My name is Sophie and I'm a purple cat!
```

#### Solution

```Ruby
class Cat
  COLOR = 'purple'

  attr_reader :name

  def initialize(name)
    @name = name
  end  

  def greet
    puts "Hello! My name is #{name} and I'm a #{COLOR} cat!"
  end
end

kitty = Cat.new('Sophie')
kitty.greet
```

### Exercise 7: Identifying yourself (part 2)

Update the following code so that it prints `I'm Sophie!` when it invokes `puts kitty`.

```ruby
class Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end
end

kitty = Cat.new('Sophie')
puts kitty

# Expected ouput
# I'm Sophie!
```

#### Solution

```ruby
class Cat
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def to_s
    "I'm #{name}"
  end
end

kitty = Cat.new('Sophie')
puts kitty
```

### Exercise 8: Public Secret

Using the following code, create a class named `Person` with an instance variable named `@secret`. Use a setter method to add a value to `@secret`, then use a getter method to print `@secret`.

```ruby
person1 = Person.new
person1.secret = 'Shh.. this is a secret!'
puts person1.secret

# Expected output
# Shh.. this is a secret!
```

#### Solution

```ruby
class Person
  attr_accessor :secret
end

person1 = Person.new
person1.secret = 'Shh.. this is a secret!'
puts person1.secret
```

### Exercise 9: Private Secret

Using the following code, add a method named share_secret that prints the value of @secret when invoked.

```ruby
class Person
  attr_writer :secret

  private

  attr_reader :secret
end

person1 = Person.new
person1.secret = 'Shh.. this is a secret!'
person1.share_secret

# Expected output
# Shh.. this is a secret!
```

#### Solution

```ruby
class Person
  attr_writer :secret

  def share_secret
    puts secret
  end

  private

  attr_reader :secret

end

person1 = Person.new
person1.secret = 'Shh.. this is a secret!'
person1.share_secret
```

### Exercise 10: 

Using the following code, add an instance method named `compare_secret` that compares the value of `@secret` from `person1` with the value of `@secret` from `person2`.

```ruby
class Person
  attr_writer :secret

  protected

  attr_reader :secret
end

person1 = Person.new
person1.secret = 'Shh.. this is a secret!'

person2 = Person.new
person2.secret = 'Shh.. this is a different secret!'

puts person1.compare_secret(person2)

# Expected output
# false
```

#### Solution

```ruby
class Person
  attr_writer :secret

  def compare_secret(other_person)
    secret == other_person.secret
  end

  protected

  attr_reader :secret
end

person1 = Person.new
person1.secret = 'Shh.. this is a secret!'

person2 = Person.new
person2.secret = 'Shh.. this is a different secret!'

puts person1.compare_secret(person2)
```